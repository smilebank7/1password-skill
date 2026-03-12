# op CLI Examples

## List & Search

```bash
# List all vaults
op vault list

# List items in a vault
op item list --vault <vault-name>
op item list --vault <vault-name> --format json

# Search by tags or categories
op item list --vault <vault-name> --categories login
op item list --vault <vault-name> --tags production
```

## Read Secrets

```bash
# By item name + field
op item get "<item-name>" --vault <vault-name> --fields credential
op item get "<item-name>" --vault <vault-name> --fields username,password

# By op:// reference
op read "op://<vault>/<item>/<field>"

# SSH key
op read "op://<vault>/<item>/private key?ssh-format=openssh"

# Output to file
op read --out-file ./key.pem "op://<vault>/<item>/private key?ssh-format=openssh"

# OTP (one-time password)
op read "op://<vault>/<item>/one-time password?attribute=otp"
```

## Run Commands with Secrets (process-scoped injection)

```bash
# Inline: secret only exists in subprocess env
GITHUB_TOKEN="op://<vault>/GitHub PAT/token" op run -- gh repo list

# From env file with op:// references
op run --env-file=".env" -- docker compose up

# Multiple secrets
DB_USER="op://<vault>/db-prod/username" \
DB_PASS="op://<vault>/db-prod/password" \
op run -- psql -h db.example.com mydb
```

## Inject into Files (template → real file)

```bash
# template.env contains: API_KEY={{ op://<vault>/My API/credential }}
op inject -i template.env -o .env

# Pipe mode
echo 'password: {{ op://<vault>/db-prod/password }}' | op inject
```

## Account & Auth

```bash
# Check current auth status
op whoami

# List configured accounts
op account list

# Link account (one-time, if not yet connected)
op signin

# Sign in to specific account
op signin --account <signin-address>
```
