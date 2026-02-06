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

### Standard Terminal

```bash
# 1. Start auth with PTY (password prompt)
hydroxide auth your@proton.me

# 2. Enter your Proton bridge password

# 3. Copy the bridge password output

# 4. Update .env.email with new bridge password
```

### OpenClaw Agent (PTY Method)

For OpenClaw agents, use the `exec` tool with `pty: true`:

```javascript
// Step 1: Start auth with PTY
exec({
  command: "hydroxide auth your@proton.me",
  pty: true
})

// Step 2: Wait for password prompt, then send password
process({
  action: "write",
  sessionId: "<session-id-from-step-1>",
  data: "your-proton-bridge-password\n"
})

// Step 3: Capture the bridge password from output
// Example output: "Bridge password: AbC123XyZ..."

// Step 4: Update .env.email with the new bridge password
write({
  path: "/path/to/.env.email",
  content: "IMAP_PASSWORD=AbC123XyZ...\nSMTP_PASSWORD=AbC123XyZ..."
})
```

**Key points:**
- `pty: true` is required - hydroxide auth needs an interactive terminal
- The bridge password is different from your Proton login password
- Store the bridge password in `.env.email`, not your login password

## Automation

### Cron Jobs

```bash
# Send notification
amail send --to user@example.com --subject "Alert" --body "Something happened"
```

### OpenClaw Agent Workflows

```javascript
// Check for unread emails
exec({
  command: "export PATH=$HOME/.local/bin:$PATH && amail list"
})

// Read specific email
exec({
  command: "amail read --id 123"
})

// Send reply
exec({
  command: `amail reply --id 123 --body "${generatedReply}" --mark-read`
})
```

### Email Agent Pattern (Sub-agent)

```javascript
// Full email agent workflow for cron jobs:

// 1. Check bridge status
exec({ command: "amail status" })

// 2. List unread emails
const listResult = exec({ command: "amail list" })

// 3. For each unread, read and respond
for (const emailId of extractIds(listResult)) {
  // Read email content
  const content = exec({ command: `amail read --id ${emailId}` })
  
  // Generate reply with model
  const reply = model({
    messages: [{ role: "user", content: `Reply to: ${content}` }]
  })
  
  // Send reply and mark read
  exec({
    command: `amail reply --id ${emailId} --body "${reply}" --mark-read`
  })
}
```

## License

MIT
