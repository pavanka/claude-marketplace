# Projection Surfaces

Use projections for broad views rather than product-native CRUD.

## Main surfaces

- `work query` — recent work across entities
- `org-tree` — management chain or reporting tree
- `focus-areas-tree` — focus area hierarchy with optional Focus subtree position counts
- `context jira workitem` — issue perimeter and nearby artifacts (code, docs, hierarchy, dependencies)

## Common shapes

```bash
scripts/twg work query --scope me --since 7d
scripts/twg work query --scope user --account-id <id> --since 30d
scripts/twg work query --first 50 --after "cursor=="

scripts/twg org-tree --name "Jane Doe" --up-only
scripts/twg org-tree --email "jdoe@example.com" --depth 5
scripts/twg org-tree --include-profile-title --include-location --include-dept

scripts/twg focus-areas-tree --name "Teamwork Graph" --depth 4
scripts/twg focus-areas-tree ari:cloud:mercury::focus-area/... --include-positions

scripts/twg context jira workitem GI-2789   # accepts issue key directly
scripts/twg context jira workitem --jql 'project = GI AND assignee = currentUser()'
scripts/twg context jira workitem GI-123 --depth 2
scripts/twg context jira workitem GI-123 --depth 1 --include code,docs

scripts/twg context jira workitem GI-123 --type repo --order-by relevance
scripts/twg context jira workitem GI-123 --fast --depth 1
```

## High-signal flags

- `--scope`
- `--account-id`
- `--since`
- `--first` / `--after`
- `--name` / `--email`
- `--depth`
- `--up-only` / `--down-only`
- `--include-positions` on `focus-areas-tree`
- `--fast` (context: skip ORS enrichment and ranking, return structured context)
- `--timeout` (context: ORS resolution timeout when not --fast)
- `--type` (context: comma-separated entity type filter, e.g. repo,doc,pr)
- `--order-by` (context: sort field — relevance, recency, relationship)
- `--limit` (context: max ranked results)

## Position counts on focus-areas-tree

- `--include-positions` adds Focus subtree position counts as `positions.count` on each shown focus area.
- Counts come from the Focus product backend, not GraphStore.
- Tree filters such as `--status`, `--depth`, `--up-only`, and `--down-only` change which nodes are shown, but they do not recompute the count on a shown node.

## Good defaults

- use `work query` for "what has this person worked on?"
- use `org-tree` for hierarchy questions
- use `focus-areas-tree --include-positions` for focus-area allocation rollups
- use `context jira workitem` to understand the perimeter around a Jira issue (hierarchy, code, docs, dependencies)
- use `context jira workitem` (default) for ranked, enriched linked content with relevance signals
- use `context jira workitem --fast` for quick structured context without ranking
- use `context jira workitem --type repo` to find which repos relate to an issue
