# 1Password CLI — Desktop App Integration

## Setup (one-time)

1. Install: `brew install 1password-cli` (macOS) or [see official docs](https://developer.1password.com/docs/cli/get-started/) for other platforms
2. Open 1Password desktop app
3. Settings > Developer > Enable "Integrate with 1Password CLI"
4. Settings > General > Enable "Keep 1Password in the menu bar" (macOS)
5. (Optional) Settings > Security > Enable Touch ID / Windows Hello / system auth

## How It Works

- `op` CLI communicates with the desktop app via IPC (XPC on macOS, named pipe on Windows, PolKit on Linux)
- First `op` command triggers biometric / password prompt
- Session auto-refreshes on each use (10 min inactivity timeout, 12 hour hard cap)
- App must be running (menu bar / system tray is sufficient, doesn't need to be foreground)

## Auth States

| App State | CLI Behavior |
|---|---|
| Running + unlocked | Works immediately |
| Running + locked | Triggers unlock prompt (Touch ID / password) |
| Not running | Fails with connection error |
| Screen locked (app still unlocked) | Works fine |

## Platform Notes

- **macOS**: Touch ID and Apple Watch unlock supported. Requires Big Sur 11.0+ for app integration.
- **Windows**: Windows Hello supported. Enable in Settings > Security.
- **Linux**: Requires PolKit + an auth agent. Settings > Security > "Unlock using system authentication".

## Troubleshooting

- "connection reset" → Open 1Password app
- "authorization required" → Unlock the app (Touch ID or password)
- "account is not signed in" → Run `op signin` once to link account, then app integration takes over
- "CLI couldn't connect to desktop app" → Update both app and CLI to latest, restart the app
