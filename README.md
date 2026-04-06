# TWG Cowork Plugin

Cowork plugin for the TWG CLI. Ships a bootstrap skill that installs the CLI,
authenticates, and installs the full TWG skill on first use.

## Structure

- `.claude-plugin/plugin.json` — plugin manifest with `userConfig` credential fields
- `skills/twg-setup/SKILL.md` — bootstrap skill (triggers on first Atlassian request)

## How it works

1. The bootstrap skill (`skills/twg-setup/SKILL.md`) triggers on the first Atlassian-related request.
2. If `twg` is not installed, it curls the public installer (`--skip-login --skip-skills --yes`). If already installed, it runs `twg update --yes`.
3. It authenticates headlessly via `twg login` using plugin credentials, then runs `twg skills install --target claude`.
4. The full TWG skill is installed at `~/.claude/skills/twg/` and takes over from the next session.

## Credentials

Prompted at plugin install time and stored in the OS keychain. Injected as `CLAUDE_PLUGIN_OPTION_*` env vars into subprocesses.

| Field             | Sensitive | Description                                         |
|-------------------|-----------|-----------------------------------------------------|
| `atlassian_email` | no        | Atlassian account email                             |
| `atlassian_token` | yes       | Atlassian API token                                 |
| `atlassian_site`  | no        | Site prefix (e.g. `hello` for hello.atlassian.net)  |
| `bitbucket_token` | yes       | Bitbucket token for `twg bb` commands (optional)    |
| `rovo_mcp_token`  | yes       | Rovo MCP token for `twg rovo search` (optional)     |

## Local run

```bash
claude --plugin-dir ./resources/plugins/cowork-plugin
```

## Notes

- This plugin does **not** use MCP.
- No bundled runtime — the bootstrap skill uses the public CDN installer.
