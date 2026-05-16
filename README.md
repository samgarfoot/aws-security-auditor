![Python](https://img.shields.io/badge/python-3.x-blue)
![Platform](https://img.shields.io/badge/platform-macOS-lightgrey)
![License](https://img.shields.io/badge/license-MIT-green)
![AWS](https://img.shields.io/badge/AWS-eu--west--2-orange)

# AWS Security Auditor

A Python-based automated security auditing tool for AWS environments, mapping findings to CIS Critical Security Controls and the NIST Cybersecurity Framework. Built as part of a home lab infrastructure project to demonstrate real-world security engineering practices.

---

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

---

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

---

## Screenshots

### 1. Tool Home Screen
![home_screen](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/01_home_screen.png)
> The CLI banner displays on launch showing the tool name, services covered, active frameworks, and live stats including the last security score, scan interval, and number of saved reports. A colour legend shows the severity indicator colours used throughout the tool.

---

### 2. Interactive Menu
![main_menu](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/02_main_menu.png)
> After confirming the audit the numbered menu presents all available options. Engineers can run a single scan, start continuous monitoring, acknowledge findings, view saved reports, reset scan data, or access the built-in help and configuration editor.

---

### 3. Scan Terminal Output
![terminal](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/03_scan_terminal.png)
> The scanner runs checks across all configured AWS services in sequence, displaying real-time PASS and FAIL results as each check completes. The audit summary at the bottom shows total checks, passed, failed, critical and high counts, along with full remediation steps for every failed finding.

---

### 4. HTML Audit Report — Before Remediation
![1st_audit](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/04_audit1.png)
> The first audit report showing the initial security posture of the environment. The score card shows a Poor rating with findings colour coded by severity — Critical in red, High in amber, and Pass in green. Each failed finding includes a specific remediation step.

---

### 5. CIS Compliance Report — Before Remediation
![compliance-1](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/05_compliance.png)
> The formal CIS and NIST compliance report generated alongside the audit report. Each CIS Critical Security Control assessed is shown as Compliant or Non-Compliant, with NIST CSF Protect and Detect functions mapped below. Non-compliant controls are highlighted in red.

---

### 6. Automated Alert Email
![email](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/06_email.jpeg)
> When Critical or High findings are detected the tool automatically sends a consolidated alert email to the configured recipient. The subject line includes the current security score and finding counts. The email body separates Critical and High findings with full remediation steps, and the HTML audit report and CIS compliance report are attached for immediate review.

---

### 7. HTML Audit Report — After Remediation
![audit-2](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/07_audit2.png)
> The second audit report following remediation of the identified findings. The security score has improved significantly, moving from Poor to a higher rating as critical and high severity issues were resolved. The findings table now shows more passing checks across IAM, S3, EC2, and CloudTrail.

---

### 8. Changes Since Last Scan
![system_changes](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/08_audit_changes.png)
> The changes section at the bottom of the HTML report compares the current scan against the previous one. Resolved findings are highlighted in green and new findings in red, with the score change shown as a point difference. This drift detection capability mirrors the functionality found in enterprise security tools such as AWS Security Hub.

---

### 9. CIS Compliance Report — After Remediation
![compliance_report2](https://github.com/samgarfoot/aws-security-auditor/blob/main/screenshots/09_compliance.png)
> The updated compliance report following remediation shows additional CIS controls moving to Compliant status. The summary cards at the top reflect the improved compliance posture across both CIS Critical Security Controls and the NIST Cybersecurity Framework Protect and Detect functions.

---

## Security Score

The tool calculates an overall security posture score out of 100:

- CRITICAL finding — -20 points
- HIGH finding — -10 points
- LOW finding — -2 points
- Scan error — -5 points

| Score | Rating |
|-------|--------|
| 80-100 | Good |
| 60-79 | Fair |
| Below 60 | Poor |

---

## Prerequisites

- Python 3.x
- AWS CLI configured with appropriate credentials
- boto3 installed (`pip3 install boto3`)
- An IAM user with at minimum `PowerUserAccess` and `IAMReadOnlyAccess`
- A Gmail account with an App Password for alert emails

---

## Installation

```bash
git clone https://github.com/samgarfoot/aws-security-auditor.git
cd aws-security-auditor
pip3 install -r requirements.txt
```

Configure your environment variables:

```bash
echo 'export AWS_AUDITOR_EMAIL="your.sender@gmail.com"' >> ~/.zshrc
echo 'export AWS_AUDITOR_PASSWORD="yourapppassword"' >> ~/.zshrc
echo 'export AWS_AUDITOR_RECIPIENT="your.recipient@email.com"' >> ~/.zshrc
echo 'export AWS_AUDITOR_ESCALATION_EMAIL="your.escalation@email.com"' >> ~/.zshrc
source ~/.zshrc
```

---

## Usage

```bash
python3 aws_auditor.py
```

**CLI Menu:**
- [1] Run single scan
- [2] Start continuous monitoring
- [3] Acknowledge all findings
- [4] View last report
- [5] Reset scan data
- [6] Exit
- [0] Help / Configure

## Alert Flow
- Scan detects Critical/High findings
- Consolidated email sent to primary recipient
- HTML audit report + CIS compliance report attached
- If unacknowledged after threshold → escalation email to secondary recipient
- Engineer acknowledges via CLI command
- Escalation stops

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
