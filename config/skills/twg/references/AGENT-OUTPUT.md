# Agent output contract

Summary adapts by payload size. Ends with `---END---` (missing = truncated). Full JSON always at `output_files.stdout`.

- **Small** (< 1 KB): `stdout_inline` — full payload inlined, no schema
- **Large** (≥ 1 KB): `stdout_stats` (distinct structural field counts) + `stdout_shape` (merged schema+samples tree)

## Small payload (mutation result, single entity GET)

```yaml
output_files:
  stdout: /tmp/twg/.../stdout.json
  stdout_lines: 7
  stdout_bytes: 245
command: "project update"
resource_type: "atlas:project"
stdout_inline:
  apiVersion: "v2"
  command: "project update"
  data:
    ok: true
    id: "ari:cloud:townsquare:123:project/456"
  meta:
    resourceClass: "atlas:project"
---END---
```

## Large payload (list result)

```yaml
output_files:
  stdout: /tmp/twg/.../stdout.json
  stdout_lines: 1050
  stdout_bytes: 39779
command: "jira workitem list"
resource_type: "jira:workitem"
stdout_stats:
  top_level_keys: [apiVersion, command, data, meta]
  array_fields: 1
  max_array_length: 50
  total_string_fields: 14
  total_leaf_fields: 16
stdout_shape:
  data:
    items:
      T: A
      L: 50
      I:
        assignee: ["S|Z", 6, 40, ["John Doe", "Jane Smith"]]
        key: [S, 7, 50, ["GI-2800", "GI-2801"]]
        priority: [S, 5.2, 50, ["High", "Medium"]]
        status: [S, 7.3, 50, ["In Progress", "Done"]]
    total: N
---END---
```

## How to read it

- `output_files` — all file paths available to the agent summary
- `output_files.stdout` — the full JSON output file
- `output_files.stdout_lines` — line count of the JSON file
- `output_files.stdout_bytes` — byte size of the JSON file
- `command` — the CLI command string, promoted to top level for fast triage
- `resource_type` — the resource class or type, promoted to top level
- `stdout_inline` — full JSON payload as YAML (small payloads only); when present, `stdout_shape` and `stdout_stats` are omitted
- `stdout_stats` — one-glance structural summary (large payloads only); answers "is this a list?" and "how big?" without parsing the shape
- `stdout_shape` — merged schema+samples tree (large payloads only); covers the data payload with inline string samples
- `---END---` — terminator line; signals end of output for truncation detection

## Schema abbreviations

- `S` = string
- `N` = number
- `B` = boolean
- `Z` = null
- `A` = array

Objects are implicit: if a node is a mapping without `T`, it is an object shape.

For arrays:
- `T: A` — array
- `L` — observed array length; range notation if heterogeneous (e.g. `"2..3"` means sizes from 2 to 3)
- `I` — item schema

Undefined object fields are omitted entirely. Undefined array items are treated as `Z` because JSON serialization turns them into `null`.

## Shape format for string leaves

String leaves in `stdout_shape` are inline tuples combining type + sample data:

```yaml
field: [S, avg_length, count, [example1, example2]]
```

Where:
- `S` (or `S|Z`, etc.) — type descriptor
- `avg_length` — average string length for that leaf
- `count` — number of observed values
- `[example1, example2]` — nested list of safe, truncated examples; the nesting makes the boundary between metadata and samples unambiguous

Non-string leaves remain as bare type markers (`N`, `B`, `Z`).

## Stats format

```yaml
stdout_stats:
  top_level_keys: [apiVersion, command, data, meta]
  array_fields: 1          # number of distinct array fields
  max_array_length: 50     # largest observed array
  total_string_fields: 14  # unique string leaf paths
  total_leaf_fields: 16    # all leaf paths (strings + numbers + booleans + nulls)
```

Use `stdout_stats` to answer "is this a list result or a single entity?" and "how big is the data?" without parsing `stdout_shape` at all.

## Strategy guide

| `stdout_bytes` | Recommended approach |
|----------------|----------------------|
| < 1024         | `stdout_inline` present — read data directly from summary |
| < ~5000        | Open `output_files.stdout` directly — small enough to read in full |
| ≥ ~5000        | Use `stdout_shape`/`stdout_stats` to find field paths, then open or `jq`-filter the file |

## Deep-analysis requirement

**The YAML summary is a structural hint, not the answer.** You MUST follow up:

- **Small payload** (`stdout_inline` present): answer directly from the inlined data.
- **Large payload** (`stdout_shape`/`stdout_stats` present): open or filter `output_files.stdout` to get exact values, URLs, assignees, counts, or any detail the user needs. **Do not stop at the summary stats** — knowing "42 items, status ∈ {In Progress, Done}" is not enough to answer "which items are blocked?".

```bash
# Typical follow-up
jq '.data.edges[].node | {key, summary, status: .status.name, url: .webUrl}' \
  /tmp/twg/<session>/stdout.json
# For very large files (> 50 KB), use targeted jq rather than opening the full file
```

## Notes

- `stdout_shape` tuples `[S, avg_len, count, [ex1, ex2]]` are lossy hints — samples are not exhaustive.
- `command` and `resource_type` at the top level are the fastest way to triage what kind of result you received.
- Treat stderr as advisory text, not part of the structured agent protocol.
