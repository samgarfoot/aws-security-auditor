## Configuration Settings

Settings can be changed from within the tool via **option 0 → Help / Configure**, or by editing the top of `aws_auditor.py`:

| Setting | Default | Description |
|---------|---------|-------------|
| `SCAN_INTERVAL_MINUTES` | 30 | How often continuous scans run |
| `CRITICAL_ESCALATION_MINUTES` | 5 | Time before critical findings escalate |
| `HIGH_ESCALATION_MINUTES` | 15 | Time before high findings escalate |
| `AWS_REGION` | eu-west-2 | AWS region to audit |

Email recipients are stored as environment variables on your machine for security. To update them:

| Variable | Purpose | How to update |
|----------|---------|---------------|
| `AWS_AUDITOR_EMAIL` | Gmail account used to send alerts | Edit `~/.zshrc` |
| `AWS_AUDITOR_PASSWORD` | Gmail app password | Edit `~/.zshrc` |
| `AWS_AUDITOR_RECIPIENT` | Primary alert recipient | Edit `~/.zshrc` or via tool config editor |
| `AWS_AUDITOR_ESCALATION_EMAIL` | Escalation recipient | Edit `~/.zshrc` or via tool config editor |

> **Note:** After editing `~/.zshrc` always run `source ~/.zshrc` to apply changes.
