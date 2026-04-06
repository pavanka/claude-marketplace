---
name: twg-setup
description: >
  Bootstrap the TWG CLI Cowork plugin. Installs or updates the CLI, authenticates with
  Atlassian credentials, and installs the full TWG skill. Run once on first use.
---

# TWG — Setup

## Check whether setup is needed

```bash
twg --version 2>/dev/null && ls ~/.claude/skills/twg/SKILL.md 2>/dev/null && echo "ready" || echo "setup needed"
```

If already ready, tell the user to start a new session to load the full TWG skill.

## Steps (run once)

### 1. Install or update the CLI

```bash
if command -v twg >/dev/null 2>&1; then
  twg update --yes
else
  bash <(curl -fsSL https://teamwork-graph.atlassian.com/cli/install) --yes --skip-login --skip-skills
  export PATH="${HOME}/.local/bin:${PATH}"
fi
```

### 2. Authenticate

```bash
TWG_USER="${CLAUDE_PLUGIN_OPTION_ATLASSIAN_EMAIL}" \
TWG_TOKEN="${CLAUDE_PLUGIN_OPTION_ATLASSIAN_TOKEN}" \
TWG_SITE="${CLAUDE_PLUGIN_OPTION_ATLASSIAN_SITE}" \
TWG_BBC_TOKEN="${CLAUDE_PLUGIN_OPTION_BITBUCKET_TOKEN}" \
TWG_MCP_TOKEN="${CLAUDE_PLUGIN_OPTION_ROVO_MCP_TOKEN}" \
  twg login
```

### 3. Install the full skill

```bash
twg skills install --target claude
```

### 4. Done

Tell the user setup is complete and to start a new session to load the full TWG skill.
