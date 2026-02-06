# amail - Proton Mail CLI for Hydroxide

A simple, clean CLI wrapper for Proton Mail using the [hydroxide](https://github.com/emersion/hydroxide) open-source bridge.

## Installation

```bash
# Clone the repo
git clone git@github.com:ash-openclaw/amail-cli.git
cd amail-cli

# Make executable
chmod +x amail

# Optional: add to PATH
sudo ln -sf $(pwd)/amail /usr/local/bin/amail
```

## Prerequisites

1. **Install hydroxide** (Go 1.24+ required):
```bash
go install github.com/emersion/hydroxide/cmd/hydroxide@latest
```

2. **Configure credentials** in `.env.email`:
```bash
PROTON_BRIDGE_USER=you@proton.me
IMAP_PASSWORD=your-bridge-password
SMTP_PASSWORD=your-bridge-password
```

3. **Start hydroxide**:
```bash
hydroxide -disable-carddav serve
```

## Usage

```bash
# Check bridge status
amail status

# List unread emails
amail list

# Read specific email
amail read --id 123

# Send email
amail send --to user@example.com --subject "Hello" --body "Message"

# Reply to email
amail reply --id 123 --body "Thanks!" --mark-read

# Mark emails as read
amail mark-read --ids 123 124

# Search emails
amail search --from "chris"
```

## Authentication

To authenticate with Proton:

```bash
# 1. Start auth with PTY (password prompt)
hydroxide auth your@proton.me

# 2. Enter your Proton bridge password

# 3. Copy the bridge password output

# 4. Update .env.email with new bridge password
```

## Automation

For cron jobs or automation:

```bash
# Send notification
amail send --to user@example.com --subject "Alert" --body "Something happened"
```

## License

MIT
