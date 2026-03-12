# 1password-skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for 1Password CLI (`op`) integration.

Uses **desktop app integration** for seamless authentication — no manual `op signin`, no tmux hacks, no session token management. Just Touch ID once per session.

## Install

Clone directly into your Claude Code skills directory:

```bash
git clone https://github.com/smilebank7/1password-skill.git ~/.claude/skills/1password
```

To update:

```bash
git -C ~/.claude/skills/1password pull
```

## Prerequisites

1. [1Password CLI](https://developer.1password.com/docs/cli/get-started/) installed
2. 1Password desktop app running
3. App Settings > Developer > **"Integrate with 1Password CLI"** enabled
4. (Optional) Touch ID / Windows Hello / system auth enabled

## What It Does

Teaches Claude Code how to use 1Password CLI properly:

- **List** vault items (metadata)
- **Read** secrets via `op item get` or `op read`
- **Run** commands with secrets injected via `op run` (secrets stay in subprocess only)
- **Inject** secrets into file templates via `op inject`
- **Handle errors** gracefully (locked app, missing items, connection issues)

## How Auth Works

```
First op command → Touch ID (one time) → auto-refreshes for up to 12 hours
```

No `op signin` ceremony. No tmux sessions. The desktop app handles everything via IPC.

## File Structure

```
1password-skill/
├── SKILL.md                    # Skill definition (frontmatter + instructions)
├── references/
│   ├── get-started.md          # Setup guide & troubleshooting
│   └── cli-examples.md         # op CLI command reference
├── README.md
└── LICENSE
```

## Security Notes

- This skill allows the AI to **read secret values** via `op item get` / `op read`. If you need the AI to use secrets without seeing them, use `op run` to inject secrets into subprocesses.
- The AI is instructed to never log or echo secret values, but this is a behavioral guardrail, not a technical one.
- All authentication goes through the 1Password desktop app — no tokens are stored in config files.

## License

MIT
