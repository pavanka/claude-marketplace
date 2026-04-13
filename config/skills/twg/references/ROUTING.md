# Routing

Use this guide to pick the right surface first.

## Default order

1. **Federated** for cross-product requests: `docs`, `videos`, `meetings`, `spaces`
2. **Native** for product-specific detail or mutations: `jira`, `confluence`, `bb`, `goals`, `projects`, `teams`
3. **Projection** for broad activity, hierarchy, or perimeter: `work query`, `org-tree`, `context`
4. **Cypher** only if the typed surfaces do not cover the request

## Jira vs JSM

Prefer `jira` for overlapping issue-centric nouns, even when the project is a JSM project.

- Use `jira` for: `workitem`, `space`, `board`, `dashboard`, `field`, `sprint`, `filter`
- Use `jsm` for: portals, request types, queues, help-center content, knowledge resources, and service/incident/service-tier lookups

If the user says "JSM ticket" but wants the underlying issue record, comments, transitions, or status, route to Jira:

```bash
scripts/twg jira workitem get --id PROJ-123 --site <site>
scripts/twg jira workitem query --site <site> --jql 'project = PROJ'
```

Use JSM only when the user is asking about service-management objects rather than the Jira issue record:

```bash
scripts/twg jsm request-type query --site <site> --query-term "password"
scripts/twg jsm incident get <id-or-key> --site <site>
```

## Query then get

When both exist, search first and fetch one item second.

```bash
scripts/twg teams query --query Platform
scripts/twg teams get "Platform Engineering"

scripts/twg docs query --since 7d
scripts/twg docs get <id-or-ari>
```

## Typical pivots

```bash
scripts/twg docs query --since 14d                              # federated → pivot to native:
scripts/twg confluence page get 12345 --body-format adf

scripts/twg context jira workitem PROJ-123 --type repo          # perimeter → native detail:
scripts/twg jira workitem get --id PROJ-123

scripts/twg jsm request-type query --query-term "password"      # JSM intake/config
scripts/twg context jira workitem PROJ-123 --fast               # quick structured context
```

## Bitbucket repo contributor ranking

When the user asks who contributed most to a repository and a local checkout or explicit repo path is available, use the direct leaf command rather than composing `git shortlog` probes:

```bash
scripts/twg bb repo contributors --repo-path <path> --range main..HEAD
scripts/twg bb repo contributors --repo-path <path> --ref HEAD
```

If the question is about a remote repository and no local checkout is available yet, do not route here by default. Stay on the normal Bitbucket repo/PR surfaces until you have a checkout path to inspect.
