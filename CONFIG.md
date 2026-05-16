## Configuration Settings

Settings can be changed from within the tool via:

→ option 0 

→ Help / Configure or by editing the top of aws_auditor.py:

- SettingDefaultDescription - SCAN_INTERVAL_MINUTES 30, How often continuous scans run
- CRITICAL_ESCALATION_MINUTES - 5, Time before critical findings escalate
- HIGH_ESCALATION_MINUTES - 15, Time before high findings escalate
- AWS_REGION - eu-west-2, AWS region to audit
