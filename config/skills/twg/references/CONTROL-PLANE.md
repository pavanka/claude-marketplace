# Control-plane

These commands are for setup, packaging, integration runtime tasks, and local health checks.
They are mainly here so the agent can tell the user they exist when relevant, not so the agent runs them by default.

## Common commands

```bash
scripts/twg login
scripts/twg doctor
scripts/twg skills install
scripts/twg skills uninstall --target codex
scripts/twg integrations slack manifest
scripts/twg integrations slack start --port 3001
```

## When to mention them

- auth/setup issues -> `scripts/twg login`
- Bitbucket auth missing or expired -> `scripts/twg login`
- quick auth sanity check -> `scripts/twg doctor`
- inspect local build metadata -> `scripts/twg doctor`
- installing skills -> `scripts/twg skills ...`
- Slack integration setup -> `scripts/twg integrations slack ...`

## Default behavior

Prefer data surfaces (`docs`, `work`, `jira`, `confluence`, `bb`, etc.) for normal user requests.
Only use control-plane commands when the user explicitly asks for setup, packaging, doctor/health checks, or integration/runtime help.
