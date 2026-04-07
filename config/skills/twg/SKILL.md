---
name: twg
description: >
  Query and manage Atlassian work data across all products — Jira issues, boards, sprints and workflows;
  Confluence pages, blogs, spaces and search; Atlas goals, projects and focus areas;
  Bitbucket repos, PRs, pipelines and deployments; JSM service management, incidents and knowledge;
  teams, users, org hierarchy and collaborators; cross-product work activity, recently viewed items,
  and federated document search. Use when the user mentions any Atlassian product, work tracking,
  or developer workflow.
compatibility: macOS (arm64, x64), Linux (x64)
labels:
  - twg
---

# twg

## Overview

Run the TWG wrapper script that ships with this skill to retrieve or update Atlassian work data.
Start with federated surfaces for cross-product requests, native surfaces for product-specific detail or mutations, and projection surfaces for activity, hierarchy, and issue context.
For very large goal exports, prefer `--sqlite-file`.

Important: when a skill loader says to run `scripts/twg`, resolve that path relative to this skill directory, not relative to the caller's current working directory. If `scripts/twg` is not found from the current directory, first locate and run the wrapper relative to `SKILL.md`.

## Available scripts

- `scripts/twg` — stable TWG entrypoint for the skill, resolved relative to this skill directory

## Prerequisites

- **macOS** (arm64 or x64) or **Linux** (x64)
- TWG installed via the normal setup flow
- Auth via `twg login` (preferred) or `TWG_USER` + `TWG_TOKEN` for automation
- All tokens (API, Bitbucket, Rovo MCP) are configured during `twg login`
- A default site is set during login — only pass `-s <site>` to query a different site

## Context

Your Atlassian Account ID (AAID) is available in the system prompt — use it directly for `--account-id` flags. Do not run `doctor` to discover it. A default site is configured during `twg login` — do not pass `--site` unless the user explicitly asks to query a different site.

## Workflow

1. **Route** — check the Common patterns below first. If the command you need is listed there, use it directly. Otherwise open `references/COMMANDS.md` for the full key-command index. Use `--help` only when the needed subcommand is not listed in either place, or when the surface is intentionally lazy.
2. **Execute** — run the resolved skill-local wrapper directly. Always append `--mode agent` to every command. For large results, add `--output-file <path>` to avoid truncation.
3. **Summarize** — extract the key IDs, titles, statuses, URLs, and owners for the user.
4. **Writes** — state intended changes before write operations unless the user explicitly asked to execute them.

## Command discovery rules

- **DO** check Common patterns and `references/COMMANDS.md` FIRST for command names and flags.
- **DO** use the exact flags shown in COMMANDS.md — do NOT guess or invent flag names.
- **DO NOT** run `--help` on a command whose full syntax is already in COMMANDS.md.
- **DO NOT** walk the help tree (e.g. `twg jira --help` → `twg jira board --help` → …). Go directly to the leaf command.
- **ONLY** use `--help` when the command is not in COMMANDS.md, or you need an option not listed there.

## ID format rules

- Jira issues: use the issue KEY (e.g. `GI-1234`), not bare numeric IDs.
- When a command expects `--id` or `--issue-id`, pass the key or full ARI from prior results — never a raw number.
- Board, sprint, and filter IDs: use the numeric IDs as returned by search/query results.
- If you need an ARI for a person, team, project, or any Atlassian entity and don't already have one, use `scripts/twg resolve` to look it up. Works with names, teams, URLs, and natural-language descriptions (e.g. `scripts/twg resolve "Alice" --site <site>`, `scripts/twg resolve "core design team"`, `scripts/twg resolve "https://example.atlassian.net/wiki/spaces/ENG/pages/123456/My+Page"`).

## Tool routing — CLI only

When this skill is loaded, use the TWG CLI as the primary interface.

**Routing rules:**

1. For any query about work, goals, projects, teams, users, orgs, or collaboration → use `scripts/twg`
2. For Bitbucket: use `scripts/twg bb` commands
3. For overlapping Jira vs JSM entities, prefer `scripts/twg jira ...` for issue records, boards, dashboards, fields, spaces, sprints, and filters, even when the project is a JSM project
4. Use `scripts/twg jsm ...` only for JSM-native resources such as portals, request types, queues, help-center content, knowledge resources, and JSM ops service or incident flows
5. Check the **Common patterns** first, then open **`references/COMMANDS.md`** only if the command is not already shown below. Do not explore with `--help` unless the needed subcommand is missing from both.

### Common operations

| User intent                | TWG CLI command                                                        |
| -------------------------- | ---------------------------------------------------------------------- |
| Work item context          | `scripts/twg context jira workitem PROJ-123`                           |
| JSM ticket / issue record  | `scripts/twg jira workitem get --id PROJ-123`                          |
| JSM incident details       | `scripts/twg jsm incident get <id-or-key>`                             |
| JSM request types / intake | `scripts/twg jsm request-type query --query-term <text>`               |
| Work summary               | `scripts/twg work query --scope me --since 7d`                         |
| Manager / report chain     | `scripts/twg org-tree --email user@co.com --up-only`                   |
| Direct reports             | `scripts/twg org-tree --email user@co.com --depth 1`                   |
| User's teams               | `scripts/twg teams query --query name`                                 |
| Project details            | `scripts/twg projects get <key>`                                       |
| Project updates            | `scripts/twg projects get <key>`                                       |
| Goals                      | `scripts/twg goals --scope me`, `scripts/twg goals get <id>`           |
| Collaboration              | `scripts/twg collaborators --email user@co.com`                        |
| Team members               | `scripts/twg teams get "Team Name"`                                    |
| Created/owned entities     | `scripts/twg work query --scope user --account-id <id>`                |
| Recently viewed            | `scripts/twg recently-viewed --since 7d` _(current user only)_         |
| Assets object              | `scripts/twg assets object get <id>`                                   |
| Assets AQL search          | `scripts/twg assets query --aql "objectType = Server"`                 |
| Graph schema discovery     | `scripts/twg cypher -q 'MATCH (n:NodeType) RETURN n.typeName LIMIT 20'`|

## Guidance nudges

- For Confluence pages, prefer valid ADF bodies. For simple content, plain text/markdown converted to ADF is acceptable.
- For rich layout-sensitive Confluence content such as tables with structured cells, panels, status lozenges, expanders, or similar constructs, try to generate ADF directly instead of relying on markdown conversion.
- Do not generate storage XML for Confluence page create/update.
- For ADF structure, node catalog, and examples, see `references/ADF-SCHEMA.md`. For deeper per-node details, follow the URL patterns documented there.
- For graph exploration: (1) query metagraph for edges, (2) prefer typed surfaces, (3) fall back to `twg cypher` with underscore edge names. Viewed queries are only allowed for the authenticated user. See `references/METAGRAPH.md`.

## Common patterns

```bash
# Append --mode agent to every command (shown below for brevity)
# Default site from auth.conf is used automatically — omit -s unless querying a different site
scripts/twg work query --scope me --since 7d --mode agent
scripts/twg jira workitem query --jql "project = PROJ AND statusCategory != Done" --mode agent
scripts/twg context jira workitem PROJ-123 --mode agent
scripts/twg confluence page get <id> --body-format adf --full --mode agent
scripts/twg goals --scope me --mode agent
scripts/twg org-tree --name "Jane Doe" --up-only --mode agent
scripts/twg focus-areas --scope me --include-sub-focus-areas --mode agent
scripts/twg trello board get --shortlink <shortlink> --mode agent      # Trello boards, lists, cards, members, labels
scripts/twg bb pr get 42 --workspace <ws> --repo <repo> --mode agent
scripts/twg assets query --aql "objectType = Server" --mode agent
scripts/twg cypher -q 'MATCH (n:NodeType) RETURN n.typeName AS nodeType LIMIT 20' -o json --mode agent
scripts/twg goals --scope me --mode agent --output-file /tmp/goals.json  # large results → file
```

## Command Usage Patterns

**Output handling:** Most commands write JSON to a temp file and print `stdout=/tmp/twg/.../stdout.json`. Read it with `cat <path>` or `jq` — don't re-run the command.

**Agent flags** (always use):

- `--mode agent` — quieter output, smaller envelopes (strips hints/warnings)
- `--output-file <path>` — large results to file; prints `stdout=<path>`

**Common flag combos:**

- `--comments --full` — full PR/issue details with all comments
- `--body-format adf --full` — full Confluence page content
- `--output json` — structured JSON output (default for most commands)
- `--scope me` — current user's items (prefer over `--scope user --account-id <aaid>` for self). For another person, use `--scope user --account-id <their-aaid>`.
- `--since 7d` — time-bounded queries (days: `7d`, weeks: `2w`, months: `1m`)
- `--account-id <aaid>` — target another user. Commands without this flag (e.g. `recently-viewed`) always return the current user's data — do not attribute their output to someone else.

**Focus areas:** When the user asks for a hierarchy, descendants, subtree, investment rollup, or a report on a focus area and its sub-focus areas, resolve the target focus area first with `focus-areas search` or `focus-areas query`, then prefer `scripts/twg focus-areas-tree` for the hierarchy instead of stitching it together with repeated `focus-areas get/query` calls.

**Output shape:** Most commands return a JSON envelope `{ apiVersion, command, request, data }`. Lists are in `data.edges[].node`. Fields are flat on the node — use `.summary`, `.status.name`, `.webUrl`, not `.fields.summary` or `.fields.status.name`. Example: `jq '.data.edges[].node | {key, summary, status: .status.name, url: .webUrl}'`. Exception: Bitbucket and `work query` return different shapes — check the top-level keys with `jq 'keys'` first. `twg cypher` returns `{ request, response }` — results at `response.graphStore.cypherQueryV2.edges[].node.columns[]` with `key` (alias) and `value`.

**Site flag:** A default site is configured during `twg login` and stored in `~/.twg/auth.conf`. Product commands (jira, confluence, jsm, assets) will use the default site automatically — **do not pass `-s` or `--site` unless the user explicitly asks to query a different site**. Cross-product commands (work, goals, projects, focus-areas, org-tree) do not use a site flag at all.

## Query language recipes

When composing search queries, open the relevant recipe file for ready-made patterns:

- `references/JQL-RECIPES.md` — Jira queries (`twg jira workitem query --jql`)
- `references/CQL-RECIPES.md` — Confluence queries (`twg confluence search query --cql`)
- `references/TQL-RECIPES.md` — Goals & Projects queries (`twg goals --tql`, `twg projects --tql`)
- `references/AQL-RECIPES.md` — Assets queries (`twg assets query --aql`)
- `references/METAGRAPH.md` — graph ontology discovery and raw cypher patterns (`twg cypher`)

## References

- `references/COMMANDS.md` — compact index for the reference set
- `references/ROUTING.md` — how to pick the right surface
- `references/GLOBAL-CONTRACT.md` — grammar, filters, pagination, output, and mutation safety
- `references/FEDERATED-SURFACES.md` — cross-product surfaces
- `references/PROJECTION-SURFACES.md` — work, context, and org hierarchy
- `references/NATIVE-SURFACES.md` — product-specific surfaces
- `references/RELATION-AND-IDENTITY.md` — user lookup and collaborators
- `references/CONTROL-PLANE.md` — setup/package commands the agent should mention when relevant, not run by default
- `references/ADF-SCHEMA.md` — compact ADF node/mark catalog, examples, pitfalls, and deep reference URLs for generating Confluence page bodies
- `references/METAGRAPH.md` — Teamwork Graph ontology reference for `twg cypher`

<!-- BEGIN COMMAND REFERENCE -->

## Command Reference

For key command references with key flags, see [references/COMMANDS.md](references/COMMANDS.md).
That file lists core commands only; use `--help` only for lazy surfaces or when the needed subcommand is not listed there.

<!-- END COMMAND REFERENCE -->
