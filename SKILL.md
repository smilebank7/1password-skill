---
name: 1password
description: "1Password CLI (op) via desktop app integration. Use when reading secrets, injecting credentials into commands/files, or listing vault items. Triggers: 'op://', '1password', 'secret', 'credential', 'vault', 'token', 'API key'."
homepage: https://developer.1password.com/docs/cli/
---

# 1Password CLI

Desktop app integration handles auth automatically. No manual signin needed.

## Prerequisites

- `op` CLI installed (`brew install 1password-cli`)
- 1Password desktop app running (menu bar icon visible)
- App Settings > Developer > "Integrate with 1Password CLI" enabled

## How Auth Works

The CLI authenticates through the desktop app automatically.
- First `op` call in a session → Touch ID prompt (one time)
- Subsequent calls → auto-refreshed for up to 12 hours
- NO `op signin` needed. NO tmux needed. Just run `op` commands directly.

## Commands

### List items (metadata only)
```bash
op item list --vault <vault-name>
op item list --vault <vault-name> --format json
op vault list
```

### Read a secret value
```bash
op item get "<item-name>" --vault <vault-name> --fields <field>
op read "op://<vault>/<item>/<field>"
```

### Run a command with secrets injected (secret stays in subprocess only)
```bash
# Set env var as op:// reference, then run
DB_PASS="op://<vault>/db-prod/password" op run -- psql -U admin mydb

# Or use an env file with op:// references
op run --env-file=.env -- node server.js
```

### Inject secrets into file templates
```bash
# template.env contains: TOKEN={{ op://<vault>/GitHub PAT/token }}
op inject -i template.env -o .env
```

## Error Handling

| Error | Fix |
|---|---|
| "not signed in" or "authorization prompt" | 1Password app is locked → tell user to unlock it |
| "could not connect" / "connection reset" | App not running → tell user to open 1Password |
| "item not found" | Check vault name and item name with `op item list` |

If an `op` command fails, DO NOT retry in a loop. Tell the user what happened and what to do.

## Guardrails

- Never log, print, or echo secret values in chat output.
- Prefer `op run` / `op inject` when the secret only needs to reach a subprocess or file.
- Use `op item get` / `op read` when you need the value directly (e.g., to pass as a function argument).
- Always specify `--vault` to avoid ambiguity.
