# AWS Security Auditor — Command Reference

---

## Running the Tool

### Start the tool (interactive menu)
```bash
python3 aws_auditor.py
```
Opens the interactive CLI menu. Use arrow keys to navigate the intro screen and type numbers to select menu options.

---

### Run in cron/automated mode (no menu)
```bash
python3 aws_auditor.py --cron
```
Skips the interactive menu and runs a single scan directly. Used by cron scheduler for automated background scanning.

---

### Run in continuous automated mode
```bash
python3 aws_auditor.py --cron --continuous
```
Skips the menu and runs as part of continuous monitoring. Used internally by the tool when option 2 is selected.

---

### Acknowledge all findings
```bash
python3 aws_auditor.py --acknowledge
```
Marks all current findings as acknowledged. Run this in a separate terminal tab while the scanner is running to stop escalation emails being sent.

**Tip:** Open a new terminal tab with Cmd+T, navigate to the project folder, then run this command. The running scanner picks up the acknowledgement on its next escalation check.

---

## Installation

### Install dependencies
```bash
pip3 install -r requirements.txt
```
Installs boto3 and botocore. Run this once after cloning the repo.

---

### Configure environment variables
```bash
echo 'export AWS_AUDITOR_EMAIL="your.sender@gmail.com"' >> ~/.zshrc
echo 'export AWS_AUDITOR_PASSWORD="yourgmailapppassword"' >> ~/.zshrc
echo 'export AWS_AUDITOR_RECIPIENT="your.recipient@email.com"' >> ~/.zshrc
echo 'export AWS_AUDITOR_ESCALATION_EMAIL="your.escalation@email.com"' >> ~/.zshrc
source ~/.zshrc
```
Stores credentials securely as environment variables. Never put credentials directly in the script.

---

### Verify environment variables are set
```bash
echo $AWS_AUDITOR_EMAIL
echo $AWS_AUDITOR_RECIPIENT
echo $AWS_AUDITOR_ESCALATION_EMAIL
```
Prints the current values — should show your email addresses. If blank the variables are not set correctly.

---

## Updating Email Recipients

### Change the primary alert recipient
```bash
nano ~/.zshrc
```
Find the line `export AWS_AUDITOR_RECIPIENT="..."` and update the email address, then run:
```bash
source ~/.zshrc
```
Verify it updated:
```bash
echo $AWS_AUDITOR_RECIPIENT
```

---

### Change the escalation recipient
```bash
nano ~/.zshrc
```
Find the line `export AWS_AUDITOR_ESCALATION_EMAIL="..."` and update the email address, then run:
```bash
source ~/.zshrc
```
Verify it updated:
```bash
echo $AWS_AUDITOR_ESCALATION_EMAIL
```

---

### Change the sender Gmail account
```bash
nano ~/.zshrc
```
Update both lines:
```
export AWS_AUDITOR_EMAIL="new.sender@gmail.com"
export AWS_AUDITOR_PASSWORD="newapppassword"
```

Then run:
```bash
source ~/.zshrc
```

**Tip:** The sender Gmail account requires a Google App Password — not your regular Gmail password. Go to myaccount.google.com → Security → 2-Step Verification → App passwords to generate one. Name it AWS Security Auditor.

---

### Change recipients within the tool
You can also update recipient emails directly from the CLI without editing files:

```
python3 aws_auditor.py
```
→ Yes → 0 (Help/Configure) → Y → select recipient → Enter new email → Q to save

Changes saved to `config.json` and take effect immediately.

## AWS CLI Commands

### Configure AWS credentials
```bash
aws configure
```
Sets up your AWS access key, secret key, region and output format. Run this once when setting up the tool.

**Tip:** Use an IAM user with PowerUserAccess and IAMReadOnlyAccess — never use root credentials here.

---

### Verify AWS credentials are working
```bash
aws sts get-caller-identity
```
Returns your AWS account ID and IAM user ARN. Use this to confirm your credentials are configured correctly before running a scan.

---

### Enable EC2 detailed monitoring (both instances)
```bash
aws ec2 monitor-instances --instance-ids i-04413e20b29f208f7 i-0dfb01cedfecf690a --region eu-west-2
```
Enables detailed CloudWatch monitoring on your EC2 instances. Fixes the LOW monitoring finding in the audit report.

---

### Check a specific security group
```bash
aws ec2 describe-security-groups --group-ids sg-0354cccc30e6cdb99 --region eu-west-2
```
Returns full details of a security group including all inbound and outbound rules. Useful for investigating security group findings.

---

### List all security groups
```bash
aws ec2 describe-security-groups --region eu-west-2 --query 'SecurityGroups[*].[GroupId,GroupName]' --output table
```
Lists all security groups in your region in a clean table format.

---

### Check S3 public access block settings
```bash
aws s3control get-public-access-block --account-id YOUR_ACCOUNT_ID
```
Returns the current S3 public access block configuration at account level.

---

### List CloudTrail trails
```bash
aws cloudtrail describe-trails --region eu-west-2
```
Lists all CloudTrail trails in your region. Use this to verify your trail was created correctly.

---

### Check CloudTrail logging status
```bash
aws cloudtrail get-trail-status --name infra-project-trail --region eu-west-2
```
Shows whether your CloudTrail trail is actively logging.

---

### List VPCs
```bash
aws ec2 describe-vpcs --region eu-west-2 --query 'Vpcs[*].[VpcId,Tags[?Key==`Name`].Value|[0],IsDefault]' --output table
```
Lists all VPCs in your region with their names and whether they are the default VPC.

---

### Check VPC flow logs
```bash
aws ec2 describe-flow-logs --region eu-west-2
```
Lists all VPC flow logs in your region. Use this to verify flow logs were created correctly.

---

## Report Management

### Open latest audit report in browser
```bash
open audit_report$(ls audit_report*.html 2>/dev/null | wc -l | tr -d ' ').html
```
Opens the most recently numbered HTML audit report in your default browser.

---

### Open latest compliance report in browser
```bash
open CIS_Compliance_Report$(ls CIS_Compliance_Report*.html 2>/dev/null | wc -l | tr -d ' ').html
```
Opens the most recently numbered CIS compliance report in your default browser.

---

### List all saved reports
```bash
ls -la audit_report*.txt audit_report*.html CIS_Compliance_Report*.html 2>/dev/null
```
Lists all saved report files with their sizes and timestamps.

---

### Delete all reports and reset scan data manually
```bash
rm -f audit_report*.txt audit_report*.html CIS_Compliance_Report*.html
echo "" > last_scan_findings.json
echo "" > last_alert_times.json
echo "" > last_scan_score.json
echo "" > acknowledgements.json
```
Manually clears all scan data. Use option 5 in the menu instead — this is for emergency resets only.

---

## Cron Scheduling (coming soon)

### Add to crontab (runs every 30 minutes)
```bash
crontab -e
```
Then add:
```
*/30 * * * * cd /path/to/AWS_audit && python3 aws_auditor.py --cron --continuous
```

### View current crontab
```bash
crontab -l
```

### Remove crontab entry
```bash
crontab -r
```
**Warning:** This removes ALL cron jobs — use with caution.

---

## Troubleshooting

### Permission denied errors in scan
If you see AccessDenied errors attach IAMReadOnlyAccess to your IAM user:
```bash
aws iam attach-user-policy --user-name infra-project-admin --policy-arn arn:aws:iam::aws:policy/IAMReadOnlyAccess
```

### Verify IAM policies attached to your user
```bash
aws iam list-attached-user-policies --user-name infra-project-admin
```

### Test email sending manually
```bash
python3 -c "
import smtplib, os
from email.mime.text import MIMEText
msg = MIMEText('Test email from AWS Security Auditor')
msg['Subject'] = 'Test'
msg['From'] = os.environ.get('AWS_AUDITOR_EMAIL')
msg['To'] = os.environ.get('AWS_AUDITOR_RECIPIENT')
with smtplib.SMTP_SSL('smtp.gmail.com', 465) as s:
    s.login(os.environ.get('AWS_AUDITOR_EMAIL'), os.environ.get('AWS_AUDITOR_PASSWORD'))
    s.send_message(msg)
print('Email sent successfully')
"
```
Sends a test email to verify your Gmail app password is working correctly.

---

## Environment Variables Reference

| Variable | Purpose |
|----------|---------|
| `AWS_AUDITOR_EMAIL` | Gmail account used to send alerts |
| `AWS_AUDITOR_PASSWORD` | Gmail app password (not your Gmail password) |
| `AWS_AUDITOR_RECIPIENT` | Primary alert recipient email |
| `AWS_AUDITOR_ESCALATION_EMAIL` | Escalation recipient email |

---

## Configuration Variables (top of aws_auditor.py)

| Variable | Default | Purpose |
|----------|---------|---------|
| `SCAN_INTERVAL_MINUTES` | 30 | How often continuous scans run |
| `CRITICAL_ESCALATION_MINUTES` | 5 | Minutes before critical findings escalate |
| `HIGH_ESCALATION_MINUTES` | 15 | Minutes before high findings escalate |
| `AWS_REGION` | eu-west-2 | AWS region to audit |
