# Federated Surfaces

Start here for cross-product requests.
If the result is not rich enough, pivot to the product-native surface.

## Use these first

- `docs` — cross-product documents
- `videos` — cross-product videos
- `meetings` — meetings and recordings
- `spaces` — Jira spaces/projects by keyword or key
- `recently-viewed` — recent views across products
- `pull-requests` (alias `prs`) — one PR by ARI
- `commits` — one commit by ARI (any connected SCM)
- `deployments` — one deployment by ARI (any connected SCM)

## Common shapes

```bash
scripts/twg docs query --since 7d
scripts/twg docs get <id-or-ari>

scripts/twg videos query
scripts/twg videos get <id-or-ari>

scripts/twg meetings --account-id <id> --since 2w --with-recordings
scripts/twg meetings get <id-or-ari>

scripts/twg spaces query --keyword platform
scripts/twg spaces get GQLGW

scripts/twg recently-viewed --since 7d
scripts/twg pull-requests "ari:cloud:graph::pull-request/..."
scripts/twg commits "ari:cloud:graph::commit/..."
scripts/twg deployments "ari:cloud:graph::deployment/..."
```

## High-signal flags

- `--since`
- `--account-id`
- `--first` / `--after`
- `--with-recordings`
- `--keyword`
- `-s, --site`

## Typical pivots

```bash
# federated doc -> native Confluence details
scripts/twg docs query --since 14d
scripts/twg confluence page get 12345 --body-format adf

# PR by ARI -> repo-scoped PR workflow
scripts/twg pull-requests "ari:cloud:graph::pull-request/..."
scripts/twg bb prs get 42
```

## Notes

- `recently-viewed` has a 30-day TTL.
- Prefer query first, then get.
