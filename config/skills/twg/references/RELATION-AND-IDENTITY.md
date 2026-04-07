# Relation and Identity

Use these surfaces to resolve people first, then pivot into work, meetings, or collaboration views.

## Main surfaces

- `user` — current user, one known account ID, or `user search` for people lookup
- `collaborators` — relationship-focused view across work items

## Common shapes

```bash
scripts/twg user
scripts/twg user 557058:abc...

scripts/twg user search --name "Jane Doe"
scripts/twg user search --email "jdoe@atlassian.com"

scripts/twg collaborators --account-id <id>
```

## High-signal flags

- `--name`
- `--email`
- `--limit`
- `-s, --site`
- `--scope`

## Typical pivots

```bash
# resolve person -> inspect recent work
scripts/twg user search --name "Jane Doe"
scripts/twg work query --scope user --account-id <id> --since 30d

# resolve person -> inspect meetings
scripts/twg user search --email "jdoe@atlassian.com"
scripts/twg meetings --account-id <id> --since 2w
```
