# AWS Security Auditor

A Python-based automated security auditing tool for AWS environments, mapping findings to CIS Critical Security Controls and the NIST Cybersecurity Framework. Built as part of a home lab infrastructure project to demonstrate real-world security engineering practices.

## Features

- Multi-service scanning — audits IAM, S3, EC2, CloudTrail, GuardDuty, and VPC across your AWS account
- CIS & NIST mapping — every finding mapped to CIS Critical Security Controls and NIST CSF functions
- Security score — overall security posture score out of 100, updated on every scan
- HTML reports — colour coded audit report and formal CIS/NIST compliance report generated on every scan
- Automated alerting — consolidated Critical and High findings emailed with reports attached
- Alert deduplication — only emails when new findings are detected, preventing alert fatigue
- Escalation procedure — unacknowledged findings automatically escalated to a second recipient after a configurable threshold
- Report comparison — changes since last scan highlighted including new findings and resolved issues
- Scan error reporting — checks that fail due to permission restrictions reported separately with recommendations
- Interactive CLI — arrow key menu system with built-in help and configuration editor
- Session-aware cleanup — option to discard reports from the current session without affecting previous scans


## Checks Performed

| Service | Check | Severity | CIS Control |
|---------|-------|----------|-------------|
| IAM | Root MFA enabled | CRITICAL | Control 6 |
| IAM | Root access keys | CRITICAL | Control 6 |
| IAM | User MFA enabled | HIGH | Control 6 |
| IAM | Access key age (90 day limit) | HIGH | Control 5 |
| IAM | Password policy | HIGH | Control 5 |
| S3 | Public access block (4 settings) | HIGH | Control 3 |
| EC2 | SSH open to world | CRITICAL | Control 12 |
| EC2 | RDP open to world | CRITICAL | Control 12 |
| EC2 | Detailed monitoring | LOW | Control 8 |
| CloudTrail | Trail exists | HIGH | Control 8 |
| CloudTrail | Logging enabled | HIGH | Control 8 |
| CloudTrail | Multi-region logging | HIGH | Control 8 |
| CloudTrail | Log file validation | HIGH | Control 8 |
| GuardDuty | Enabled and active | HIGH | Control 13 |
| VPC | Flow logs enabled | HIGH | Control 13 |

## Security Score

The tool calculates an overall security posture score out of 100:

- CRITICAL finding — -20 points
- HIGH finding — -10 points
- LOW finding — -2 points
- Scan error — -5 points

ScoreRating:
- 80-100 = Good
- 60-79 = Fair
- Below 60 = Poor

## Prerequisites

- Python 3.x
- AWS CLI configured with appropriate credentials
- boto3 installed (pip3 install boto3)
- An IAM user with at minimum PowerUserAccess and IAMReadOnlyAccess
- A Gmail account with an App Password for alert emails


## Installation
- cd aws-security-auditor
- pip3 install boto3
- Configure your environment variables:
- bashecho 'export AWS_AUDITOR_EMAIL="your.sender@gmail.com"' >> ~/.zshrc
- echo 'export AWS_AUDITOR_PASSWORD="yourappppassword"' >> ~/.zshrc
- echo 'export AWS_AUDITOR_RECIPIENT="your.recipient@email.com"' >> ~/.zshrc
- echo 'export AWS_AUDITOR_ESCALATION_EMAIL="your.escalation@email.com"' >> ~/.zshrc
- source ~/.zshrc

## Usage
```bash
python3 aws_auditor.py
```

CLI Menu:
[1] Run single scan
[2] Start continuous monitoring
[3] Acknowledge all findings
[4] View last report
[5] Reset scan data
[6] Exit
[0] Help / Configure

Acknowledge findings (stops escalation):
```bash
python3 aws_auditor.py --acknowledge
```

Configuration
Settings can be changed from within the tool via option 0 → Help / Configure or by editing the top of aws_auditor.py:
SettingDefaultDescriptionSCAN_INTERVAL_MINUTES30How often continuous scans runCRITICAL_ESCALATION_MINUTES5Time before critical findings escalateHIGH_ESCALATION_MINUTES15Time before high findings escalateAWS_REGIONeu-west-2AWS region to audit

## Alert Flow
Scan detects Critical/High findings
→ Consolidated email sent to primary recipient
→ HTML audit report + CIS compliance report attached
→ If unacknowledged after threshold → escalation email to secondary recipient
→ Engineer acknowledges via CLI command
→ Escalation stops

## Reports Generated
Every scan produces three files:

- audit_reportN.txt — plain text findings and remediations
- audit_reportN.html — colour coded HTML report with score, findings table, scan errors, and changes since last scan
- CIS_Compliance_ReportN.html — formal CIS and NIST compliance report


## Security Considerations

- AWS credentials stored via environment variables — never hardcoded
- Gmail credentials stored via environment variables — never hardcoded
- IAM user follows least privilege — PowerUserAccess + IAMReadOnlyAccess only
- All report files excluded from version control via .gitignore
- Scan data and configuration files excluded from version control
