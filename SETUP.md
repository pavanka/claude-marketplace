# Setup

No manual setup needed — the bootstrap skill handles everything automatically.

## How setup works

1. Install the plugin in Cowork with your credentials (prompted at install time).
2. Start a session and ask any Atlassian-related question.
3. The bootstrap skill (`skills/twg-setup/SKILL.md`) detects that setup is needed and:
   - Downloads and installs the `twg` binary from the public CDN (or runs `twg update --yes` if already installed)
   - Runs `twg login` using the plugin credentials
   - Runs `twg skills install --target claude` to install the full skill to `~/.claude/skills/twg/`
4. Start a new session — the full TWG skill loads automatically.

## Plugin config fields (set at install time)

| Field             | Description                                                   |
|-------------------|---------------------------------------------------------------|
| `atlassian_email` | Your Atlassian account email                                  |
| `atlassian_token` | Atlassian API token (stored in OS keychain)                   |
| `atlassian_site`  | Site prefix, e.g. `hello` for `hello.atlassian.net`           |
| `bitbucket_token` | Bitbucket token for `twg bb` commands (optional)              |
| `rovo_mcp_token`  | Rovo MCP token for `twg rovo search` (optional)               |

Generate tokens:
- Atlassian API token: https://id.atlassian.com/manage-profile/security/api-tokens
- Bitbucket token: https://id.atlassian.com/manage-profile/security/api-tokens?autofillToken=&appId=bitbucket&selectedScopes=all&expiryDays=30
- Rovo MCP token: https://id.atlassian.com/manage-profile/security/api-tokens?autofillToken=&appId=mcp&selectedScopes=all&expiryDays=30

## Manual fallback

```bash
bash <(curl -fsSL https://teamwork-graph.atlassian.com/cli/install) --yes --skip-login --skip-skills
TWG_USER=you@company.com TWG_TOKEN=<token> TWG_SITE=<site> twg login
twg skills install --target claude
```
