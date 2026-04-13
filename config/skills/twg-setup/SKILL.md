---
name: twg-setup
description: >
  Bootstrap the TWG CLI Cowork plugin. Installs or updates the CLI, authenticates with
  Atlassian credentials, and installs the full TWG skill. Run once on first use.
---

# TWG — Setup

## Check whether setup is needed

```bash
twg doctor 2>/dev/null && ls ~/.claude/skills/twg/SKILL.md 2>/dev/null && echo "ready" || echo "setup needed"
```

If already ready, tell the user to just start using the twg skill

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

Read the .env file in the attached work folder and temporarily load the data into TWG_USER, TWG_TOKEN, TWG_SITE
```bash
TWG_USER="${TWG_USER}"
TWG_TOKEN="${TWG_TOKEN}" \
TWG_SITE="${TWG_SITE}" \
  twg login
```

### 3. Install the full skill

```bash
twg skills install --target claude --yes
```

### 4. Done

Tell the user setup is complete and they can now start using twg skills
