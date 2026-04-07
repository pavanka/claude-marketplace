# CQL Recipes

Common CQL patterns for `twg confluence search query --cql "..."`.

> CQL (Confluence Query Language) is used with `scripts/twg confluence search query --cql '<query>'`.
> Always pass `-s <site>` when querying a specific Atlassian site.
> CQL is for structured metadata filters and title matching — it is **not** a semantic content search.

## Table of Contents

- [By content type](#by-content-type)
- [Title matching](#title-matching)
- [By space](#by-space)
- [By creator / contributor / mention](#by-creator--contributor--mention)
- [By label](#by-label)
- [Time-based](#time-based)
- [Ancestor / hierarchy](#ancestor--hierarchy)
- [Text content search](#text-content-search)
- [Composite examples](#composite-examples)
- [Pagination and limits](#pagination-and-limits)
- [Ordering](#ordering)
- [Gotchas](#gotchas)

## By content type

```bash
# Pages only
scripts/twg confluence search query --cql 'type = page'

# Blog posts only
scripts/twg confluence search query --cql 'type = blogpost'

# Attachments
scripts/twg confluence search query --cql 'type = attachment'
```

## Title matching

```bash
# Exact title
scripts/twg confluence search query --cql 'type = page AND title = "Architecture Decision Record"'

# Title contains keyword (fuzzy)
scripts/twg confluence search query --cql 'type = page AND title ~ "runbook"'

# Title starts with
scripts/twg confluence search query --cql 'type = page AND title ~ "RFC*"'
```

## By space

```bash
# Pages in a specific space
scripts/twg confluence search query --cql 'type = page AND space = ENG'

# Pages in multiple spaces
scripts/twg confluence search query --cql 'type = page AND space in (ENG, PLATFORM, OPS)'

# Search across all spaces with title filter
scripts/twg confluence search query --cql 'type = page AND title ~ "onboarding"'
```

## By creator / contributor / mention

```bash
# Pages created by a specific user
scripts/twg confluence search query --cql 'type = page AND creator = "user@example.com"'

# Pages created by current user
scripts/twg confluence search query --cql 'type = page AND creator = currentUser()'

# Pages with contributions by a user
scripts/twg confluence search query --cql 'type = page AND contributor = "user@example.com"'

# Pages where a user is @mentioned
scripts/twg confluence search query --cql 'type = page AND mention = "user@example.com"'

# Pages where I am @mentioned
scripts/twg confluence search query --cql 'type = page AND mention = currentUser()'
```

## By label

```bash
# Pages with a specific label
scripts/twg confluence search query --cql 'type = page AND label = "architecture"'

# Pages with any of several labels
scripts/twg confluence search query --cql 'type = page AND label in ("runbook", "incident", "postmortem")'
```

## Time-based

```bash
# Recently created (last 7 days)
scripts/twg confluence search query --cql 'type = page AND created >= now("-7d")'

# Recently modified
scripts/twg confluence search query --cql 'type = page AND lastModified >= now("-7d")'

# Created this year
scripts/twg confluence search query --cql 'type = page AND created >= "2026-01-01"'
```

## Ancestor / hierarchy

```bash
# Pages under a specific parent (by page ID)
scripts/twg confluence search query --cql 'type = page AND ancestor = 12345'

# Pages in a space under a parent
scripts/twg confluence search query --cql 'type = page AND space = ENG AND ancestor = 12345'
```

## Text content search

```bash
# Pages containing specific text (metadata + title match, not deep body search)
scripts/twg confluence search query --cql 'type = page AND text ~ "deployment pipeline"'

# Include excerpts for matched text
scripts/twg confluence search query --cql 'type = page AND text ~ "deployment"' --excerpt
```

## Composite examples

```bash
# Recent runbook pages in the OPS space
scripts/twg confluence search query --cql 'type = page AND space = OPS AND label = "runbook" AND lastModified >= now("-30d")'

# My recently created pages
scripts/twg confluence search query --cql 'type = page AND creator = currentUser() AND created >= now("-14d")'

# Blog posts in a space this month
scripts/twg confluence search query --cql 'type = blogpost AND space = ENG AND created >= now("-30d")'

# Architecture docs across all spaces
scripts/twg confluence search query --cql 'type = page AND label = "architecture" AND title ~ "decision"'
```

## Pagination and limits

Use `--limit <n>` to control result count (default 25):

```bash
scripts/twg confluence search query --cql 'type = page AND space = ENG' --limit 50
```

## Ordering

CQL supports `ORDER BY` at the end of the query:

```bash
scripts/twg confluence search query --cql 'type = page AND space = ENG ORDER BY lastModified DESC'
scripts/twg confluence search query --cql 'type = page ORDER BY title ASC'
```

## Gotchas

- `text ~ "keyword"` performs a Lucene full-text search across both title and page body content. It is keyword-based (not semantic), so use specific terms rather than natural language phrases.
- Use exact title match (`title = "..."`) when you know the page name; use fuzzy (`title ~ "..."`) when browsing.
- Space keys are case-sensitive (e.g., `ENG` not `eng`).
- `ancestor` requires a numeric page ID, not a title.
