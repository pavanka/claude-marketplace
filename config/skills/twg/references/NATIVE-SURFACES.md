# Native Surfaces

Use native surfaces for product-specific detail, richer actions, and writes.

> For how these surfaces map to Atlassian product collections (TWC, ServCo, StratCo, etc.), see `references/PRODUCT-COLLECTIONS.md`.

## Main surfaces

- `jira`
- `confluence`
- `bitbucket` / `bb`
- `goals`
- `projects`
- `focus-areas`
- `teams`
- also available: `assets`, `csm`, `jsm`, `jira-align`, `talent`

## Common shapes

```bash
# Jira
scripts/twg jira workitem get --id PROJ-123
scripts/twg jira workitem create --space PROJ --type Task --summary "New task" --assignee me
scripts/twg jira workitem update --id PROJ-123 --summary "Updated title" --assignee me
scripts/twg jira workitem transition --id PROJ-123 --transition-id 21
scripts/twg jira sprint start --board-id <board-id> --id <id> --name "Sprint 42" --start-date 2026-03-01 --end-date 2026-03-14
scripts/twg jira space status query --id-or-key PROJ               # query all statuses for a project, grouped by category (use for accurate JQL)
scripts/twg jira space status query --id-or-key PROJ --output json # machine-readable output

> **JSM routing**: For incidents and service-management resources, use `twg jsm incident get`, `twg jsm service query`, or `twg jsm request-type query` rather than `twg jira workitem query`. JSM issue types are not exposed via standard Jira workitem APIs.

# Confluence
scripts/twg confluence page get 12345 --body-format adf
scripts/twg confluence page create --space-id ENG --title "Runbook" --body-file ./content.adf --body-format adf
scripts/twg confluence page update 12345 --title "Runbook" --body-file ./content.adf --body-format adf -y
scripts/twg confluence search query --cql 'type=page AND title ~ "Runbook"'

# Bitbucket
scripts/twg bb repo query
scripts/twg bb prs query
scripts/twg bb prs get 42
scripts/twg bb prs approve 42
scripts/twg bb prs create --title "My change" --source feature/branch
scripts/twg bb prs comment create 42 --text "Looks good"
scripts/twg bb prs task create 42 --text "Follow up on tests"
scripts/twg bb pipeline get 123 --logs

# Goals / projects / focus areas / teams
scripts/twg goals --scope me
scripts/twg goals --scope org --account-id <id> --include-parent-goal --include-contributing-projects
scripts/twg projects --scope me --role contributor
scripts/twg focus-areas query --owner-account-id <id> --include-linked-goals
scripts/twg teams query --query Platform
scripts/twg teams get "Platform Engineering"
```

## High-signal flags

- `--scope`
- `--role`
- `--status`
- `--name`
- `--owner-account-id` / `--owner-name` / `--owner-email`
- `--updated-since` / `--created-since`
- `--include-parent-goal`
- `--include-contributing-projects`
- `--include-linked-goals`

## Large goal exports

```bash
scripts/twg goals --scope org --account-id <id> \
  --include-parent-goal --include-contributing-projects \
  --sqlite-file /tmp/goals.db
```

## Typical pivots

```bash
# Cross-product doc -> native Confluence
scripts/twg docs query --since 14d
scripts/twg confluence page get 12345 --body-format adf

# Issue context -> native Jira
scripts/twg context jira workitem PROJ-123 --depth 2
scripts/twg jira workitem get --id PROJ-123

# Ranked linked content -> agent enrichment
scripts/twg context jira workitem PROJ-123 --type repo --order-by relevance
```

## Gotchas

- Use `confluence search query --cql ...` for structured lookup, especially when you already know a page title or want exact field filters.
- Prefer title-oriented CQL for page lookup, for example:

```bash
scripts/twg confluence search query --cql 'type=page AND title = "Runbook"'
scripts/twg confluence search query --cql 'type=page AND title ~ "Runbook"'
```

- Do **not** treat Confluence CQL as semantic content search. CQL is best for structured metadata filters and title matching, not meaning-based retrieval across page bodies.
- Semantic or fuzzy page-content search is not implemented here yet, so avoid implying that CQL provides that behavior.

## See also: focus-areas-tree

`twg focus-areas-tree` — renders the full focus area hierarchy tree for a given focus area ARI. Use this when the user wants to see parent/child relationships in the focus area structure, not just a flat list.

```bash
twg focus-areas-tree --ari <focus-area-ari>   # Show hierarchy tree
```

Routing: `PROJECTION-SURFACES.md` for full details.
