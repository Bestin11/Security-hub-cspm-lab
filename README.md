# L1-P2 — AWS Security Hub CSPM Triage Lab

## What this project is
Enabling and operating AWS Security Hub on a live AWS account to assess cloud security posture. Using CIS AWS Foundations Benchmark and AWS Foundational Security Best Practices standards to surface misconfigurations, triage findings as a security analyst, remediate confirmed issues, and document everything in professional analyst format.

---

## What I did

### 1. Enabled AWS Security Hub
- Activated Security Hub on a live AWS account
- Enabled two standards: CIS AWS Foundations Benchmark 1.4 and AWS Foundational Security Best Practices (FSBP)
- Initial CSPM score: 21% — 80 controls passed, 31 failed, 188 disabled

### 2. Assessed Cloud Security Posture
- Reviewed findings across IAM, S3, network, and monitoring controls
- Total findings surfaced: 296
- Focused triage on 10 highest priority findings across Critical, High, and Medium severities

### 3. Triaged 10 Findings
Reviewed each finding and made an analyst decision — genuine risk or false positive in this context.

| # | Finding | Severity | Decision |
|---|---------|----------|----------|
| 1 | AWS Config not enabled | Medium | Genuine — no audit trail |
| 2 | GuardDuty not enabled | High | False positive — cost decision for lab |
| 3 | Inspector not enabled | High | False positive — cost decision for lab |
| 4 | CloudTrail not multi-region | Medium | False positive — already compliant |
| 5 | VPC default security group allows all traffic | High | Genuine — lateral movement risk |
| 6 | SSM documents block public sharing disabled | Medium | Genuine — exposure risk |
| 7 | IAM password policy — no numbers required | Medium | Genuine — weak password risk |
| 8 | IAM password policy — no symbols required | Medium | Genuine — weak password risk |
| 9 | S3 bucket not enforcing SSL | Medium | Genuine — unencrypted data in transit |
| 10 | IAM password policy — minimum length not set | Medium | Genuine — weak password risk |

### 4. Remediated 5 Confirmed Findings
- **VPC default security group** — removed all inbound and outbound rules
- **SSM public sharing** — enabled block public sharing account-wide
- **IAM password policy** — set compliant policy covering length, numbers, and symbols in one action
- **S3 SSL enforcement** — added bucket policy denying all non-HTTPS requests
- **AWS Config** — enabled Config recorder with service-linked role

---

## Security Findings Summary

| Resource | Risk | Finding |
|----------|------|---------|
| VPC Default Security Group | HIGH | All traffic allowed — lateral movement risk |
| SSM Documents | MEDIUM | Public sharing not blocked |
| IAM Password Policy | MEDIUM | Missing complexity and length requirements |
| S3 Bucket | MEDIUM | HTTP requests not denied |
| AWS Config | MEDIUM | Config recorder not enabled — no audit trail |

---

## Key Concepts Demonstrated
- CSPM — assessing and scoring cloud security posture
- Security finding triage — distinguishing real risks from false positives
- CIS AWS Foundations Benchmark controls
- AWS Foundational Security Best Practices
- Least privilege and preventive vs detective controls
- Analyst-grade documentation — triage logs, posture summaries, remediation records

---

## Tools Used
AWS Security Hub, AWS Config, AWS IAM, AWS S3, AWS VPC, AWS SSM, CIS Benchmark 1.4, AWS FSBP

---

## Deliverables
- [`docs/posture-summary.md`](./docs/posture-summary.md) — CSPM score, standards, account details
- [`docs/triage-log.md`](./docs/triage-log.md) — 10 findings with analyst triage decisions
- [`docs/remediation-steps.md`](./docs/remediation-steps.md) — step-by-step remediation for each confirmed finding
