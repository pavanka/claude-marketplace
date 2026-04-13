# JQL Recipes

Common JQL patterns for `twg jira workitem query --jql "..."`.

> **Note:** Pass JQL via `--jql` flag. There is no `--limit` flag — use JQL to control result scope (e.g., `created >= -7d` or `sprint in openSprints()`).

> JQL (Jira Query Language) is used with `scripts/twg jira workitem query --jql '<query>'`.
> Always pass `-s <site>` when querying a specific Atlassian site.

## Table of Contents

- [Parent / child relationships](#parent--child-relationships)
- [Assignment](#assignment)
- [Status](#status)
- [Sprints](#sprints)
- [Time-based](#time-based)
- [Labels and components](#labels-and-components)
- [Issue types](#issue-types)
- [Priority](#priority)
- [Text search](#text-search)
- [Composite examples](#composite-examples)
- [Pagination](#pagination)
- [Ordering](#ordering)

## Parent / child relationships

```bash
# Children of an epic
scripts/twg jira workitem query --jql 'parent = PROJ-123'

# All subtasks of an issue
scripts/twg jira workitem query --jql 'parent = PROJ-123 AND issuetype = Sub-task'

# Issues in a specific epic (legacy Epic Link field)
scripts/twg jira workitem query --jql '"Epic Link" = PROJ-123'
```

## Assignment

```bash
# Unassigned issues
scripts/twg jira workitem query --jql 'project = PROJ AND assignee is EMPTY'

# Assigned to current user
scripts/twg jira workitem query --jql 'assignee = currentUser()'

# Assigned to a specific user
scripts/twg jira workitem query --jql 'assignee = "user@example.com"'

# Unassigned children of an epic
scripts/twg jira workitem query --jql 'parent = PROJ-123 AND assignee is EMPTY'
```

## Status

```bash
# Open / unresolved issues
scripts/twg jira workitem query --jql 'project = PROJ AND resolution = Unresolved'

# By specific status
scripts/twg jira workitem query --jql 'project = PROJ AND status = "In Progress"'

# By status category (To Do, In Progress, Done)
scripts/twg jira workitem query --jql 'project = PROJ AND statusCategory = "In Progress"'

# Exclude done issues
scripts/twg jira workitem query --jql 'project = PROJ AND statusCategory != Done'
```

## Sprints

```bash
# Issues in active sprints
scripts/twg jira workitem query --jql 'project = PROJ AND sprint in openSprints()'

# Issues in a named sprint
scripts/twg jira workitem query --jql 'sprint = "Sprint 42"'

# Unresolved issues in active sprints
scripts/twg jira workitem query --jql 'project = PROJ AND sprint in openSprints() AND resolution = Unresolved'
```

## Time-based

```bash
# Recently updated (last 7 days)
scripts/twg jira workitem query --jql 'project = PROJ AND updated >= -7d'

# Created this week
scripts/twg jira workitem query --jql 'project = PROJ AND created >= startOfWeek()'

# Due soon
scripts/twg jira workitem query --jql 'project = PROJ AND due <= 7d AND resolution = Unresolved'

# Overdue
scripts/twg jira workitem query --jql 'project = PROJ AND due < now() AND resolution = Unresolved'
```

## Labels and components

```bash
# By label
scripts/twg jira workitem query --jql 'project = PROJ AND labels = "backend"'

# By component
scripts/twg jira workitem query --jql 'project = PROJ AND component = "Platform"'

# Multiple labels (AND)
scripts/twg jira workitem query --jql 'project = PROJ AND labels = "backend" AND labels = "urgent"'

# Multiple labels (OR)
scripts/twg jira workitem query --jql 'project = PROJ AND labels in ("backend", "frontend")'
```

## Issue types

```bash
# Only bugs
scripts/twg jira workitem query --jql 'project = PROJ AND issuetype = Bug'

# Only stories
scripts/twg jira workitem query --jql 'project = PROJ AND issuetype = Story'

# Exclude epics
scripts/twg jira workitem query --jql 'project = PROJ AND issuetype != Epic'
```

## Priority

```bash
# High priority and above
scripts/twg jira workitem query --jql 'project = PROJ AND priority in (Highest, High)'

# Critical unresolved
scripts/twg jira workitem query --jql 'project = PROJ AND priority = Highest AND resolution = Unresolved'
```

## Text search

> ⚠️ **`text ~` requires elevated Jira permissions** and is often denied with "You do not have permission to perform the requested action." Use `summary ~` or `summary ~ ... OR description ~ ...` instead. Only fall back to `text ~` if you already know the user has full-text search access.

```bash
# PREFERRED: search summary only (works without elevated permissions)
scripts/twg jira workitem query --jql 'project = PROJ AND summary ~ "login"'

# PREFERRED: search summary OR description (broader, still safe)
scripts/twg jira workitem query --jql 'project = PROJ AND (summary ~ "login" OR description ~ "login")'

# ❌ AVOID: full-text search — often permission-denied
#    scripts/twg jira workitem query --jql 'project = PROJ AND text ~ "authentication"'
```

> **Project-scoped searches:** Always include `project = PROJ` when you know the project. Cross-project JQL (no `project =` clause) requires elevated permissions and may be denied. If you don't know the project key, use `assignee = currentUser()` or `reporter = currentUser()` instead of guessing a project key.

> **Sprint functions:** `sprint in openSprints()` and `sprint in closedSprints()` require the Agile board permission. If denied, query by date instead: `updated >= -14d AND statusCategory != Done`.

## Composite examples

```bash
# Unassigned bugs in current sprint
scripts/twg jira workitem query --jql 'project = PROJ AND issuetype = Bug AND sprint in openSprints() AND assignee is EMPTY'

# My overdue tasks
scripts/twg jira workitem query --jql 'assignee = currentUser() AND due < now() AND resolution = Unresolved'

# High-priority unresolved children of an epic
scripts/twg jira workitem query --jql 'parent = PROJ-123 AND priority in (Highest, High) AND resolution = Unresolved'
```

## Pagination

Use `--first <n>` to limit results and `--after <cursor>` for pagination:

```bash
scripts/twg jira workitem query --jql 'project = PROJ' --first 25
scripts/twg jira workitem query --jql 'project = PROJ' --first 25 --after <cursor>
```

## Ordering

JQL supports `ORDER BY` at the end of the query:

```bash
scripts/twg jira workitem query --jql 'project = PROJ ORDER BY created DESC'
scripts/twg jira workitem query --jql 'project = PROJ ORDER BY priority DESC, updated DESC'
```
