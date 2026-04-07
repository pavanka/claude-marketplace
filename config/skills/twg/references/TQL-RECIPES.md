# TQL Recipes

Common TQL patterns for `twg goals --tql "..."` and `twg projects --tql "..."`.

> TQL (Townsquare Query Language) is the query language for Atlas goals and projects.
> It is passed via `-q` / `--tql` on the `goals` and `projects` surfaces.
> Most common filters can also be applied via dedicated flags (`--status`, `--scope`, `--role`, `--tag`),
> but TQL gives finer control when those flags don't cover the need.

## Table of Contents

- [Phase (lifecycle)](#phase-lifecycle)
- [Status (health)](#status-health)
- [Owner](#owner)
- [Name search](#name-search)
- [Composite examples](#composite-examples)
- [When to use TQL vs flags](#when-to-use-tql-vs-flags)
- [TQL operators](#tql-operators)
- [Valid field values](#valid-field-values)

## Phase (lifecycle)

```bash
# Active goals (in progress) — this is the default
scripts/twg goals -q 'phase = in_progress'

# Pending goals (not started)
scripts/twg goals -q 'phase = pending'

# Completed goals
scripts/twg goals -q 'phase = done'

# Active + pending (default for goals)
scripts/twg goals -q 'phase = in_progress OR phase = pending'

# All non-archived (use --status all flag instead when possible)
scripts/twg goals --status all --updated-since 2026-01-01
```

## Status (health)

```bash
# Off-track goals
scripts/twg goals -q 'phase = in_progress AND status = off_track'

# At-risk projects
scripts/twg projects -q 'phase = in_progress AND status = at_risk'

# On-track active goals
scripts/twg goals -q 'phase = in_progress AND status = on_track'
```

## Owner

```bash
# Goals owned by current user
scripts/twg goals -q 'phase = in_progress AND owner = currentUser()'

# Equivalent shorthand using --scope me
scripts/twg goals --scope me
```

## Name search

```bash
# Goals matching a name pattern
scripts/twg goals -q 'name ~ "adoption"'

# Projects matching a name pattern
scripts/twg projects -q 'name ~ "platform"'

# Name search combined with phase
scripts/twg goals -q 'phase = in_progress AND name ~ "rovo"'
```

## Composite examples

```bash
# Active off-track goals matching a name
scripts/twg goals -q 'phase = in_progress AND status = off_track AND name ~ "migration"'

# Active projects with a name pattern
scripts/twg projects -q 'phase = in_progress AND name ~ "infrastructure"'

# Pending goals matching a keyword
scripts/twg goals -q 'phase = pending AND name ~ "Q3"'
```

## When to use TQL vs flags

| Intent | Prefer flag | Prefer TQL |
|---|---|---|
| Filter by status | `--status on_track` | — |
| Filter by scope | `--scope me` | — |
| Filter by role | `--role contributor` | — |
| Filter by tag | `-t rovo` | — |
| Filter by date | `--updated-since 2026-01-01` | — |
| Name search | — | `-q 'name ~ "keyword"'` |
| Combine phase + status + name | — | `-q 'phase = in_progress AND status = off_track AND name ~ "keyword"'` |

> **Tip:** Flags like `--status` override the TQL `phase`/`status` clauses. When using both,
> the flag takes precedence. Prefer flags for simple filters and TQL for compound queries or name search.

## TQL operators

| Operator | Meaning | Example |
|---|---|---|
| `=` | Equals | `phase = in_progress` |
| `~` | Contains / fuzzy match | `name ~ "adoption"` |
| `AND` | Logical AND | `phase = in_progress AND status = off_track` |
| `OR` | Logical OR | `phase = in_progress OR phase = pending` |

## Valid field values

**phase:** `in_progress`, `pending`, `done`, `cancelled`, `paused`

**status:** `on_track`, `off_track`, `at_risk`

**owner:** `currentUser()` or an account ID
