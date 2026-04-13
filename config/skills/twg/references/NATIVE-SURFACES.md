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
scripts/twg jira workitem create --space PROJ --type Task --summary "New task" --assignee me --priority Medium
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
scripts/twg bb repo contributors --repo-path . --range main..HEAD   # when a local checkout/path is available
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
- `--repo-path`
- `--ref`
- `--range`
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

- Use `confluence search query --cql ...` for structured lookup by title or field — not for semantic/fuzzy body search.

```bash
scripts/twg confluence search query --cql 'type=page AND title = "Runbook"'
scripts/twg confluence search query --cql 'type=page AND title ~ "Runbook"'
```

## Feedback

Submit product feedback directly from the CLI.

```bash
scripts/twg feedback --summary "Love the new notifications"
scripts/twg feedback --summary "Projects command timeout" -d "Takes 30s with 500+ projects" -t bug
scripts/twg feedback --summary "Add Slack integration" -d "Details" -t suggestion --can-contact
```

- `--summary <text>` — required short feedback title
- `-d, --description <text>` — detailed feedback
- `-t, --type <type>` — `bug`, `suggestion`, `comment`, `question`
- `--can-contact` — consent to contact/research (default: false)

Auto-collects: CLI version, OS, Node version, user identity from auth config.

**Consent rule:** always ask the user before submitting; only add `--can-contact` if they explicitly agree.

## See also: focus-areas-tree

Use `twg focus-areas-tree <ari>` (or `--name`) for parent/child hierarchy — not repeated `focus-areas get/query` calls. Key flags: `--depth`, `--up-only`, `--down-only`, `--status`, `--include-positions`. See `PROJECTION-SURFACES.md`.
