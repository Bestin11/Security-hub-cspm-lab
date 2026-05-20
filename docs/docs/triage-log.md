## Finding 1: CloudTrail.1 — Multi-Region Trail Not Enabled
Severity: High
Standard: CIS AWS Foundations Benchmark v1.2.0 (Control 2.1)
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
At least one CloudTrail trail must capture read and write management
events across all AWS regions.

Root cause:
Existing trail (management-trails) is single-region (us-east-1 only).
Activity in other regions is not logged.

False positive? No. Genuine misconfiguration.

Risk:
Attacker activity in non-monitored regions would leave no audit trail.
Blind spot for incident investigation and forensics.

Recommended action:
Edit management-trails → enable multi-region trail → enable read and
write management events.

Analyst note:
Low risk in single-region lab account. High risk in production
multi-region environments.

## Finding 2: EC2.2 — VPC Default Security Group Allows Traffic
Severity: High
Standard: CIS AWS Foundations Benchmark v1.2.0 (Control 4.3)
Status: FAIL
Resource: sg-043d0e7f66d22d6f9

What it checks:
The default security group in every VPC must have zero inbound and
zero outbound rules.

Root cause:
Default security group has permissive rules — allows inbound/outbound
traffic. This is the AWS default out-of-the-box state, never hardened.

False positive? No. Genuine misconfiguration.

Risk:
Any resource accidentally assigned the default security group inherits
open traffic rules. Common source of unintended exposure in production.

Recommended action:
Go to EC2 → Security Groups → sg-043d0e7f66d22d6f9 → delete all
inbound and outbound rules.

Analyst note:
No EC2 instances currently using this group in the lab account.
Risk is low now but must be remediated before any production workload
is deployed.

## Finding 3: Config.1 — AWS Config Not Using Service-Linked Role
Severity: Critical
Standard: CIS AWS Foundations Benchmark v1.2.0 (Control 2.5)
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
AWS Config must be enabled AND must use the AWS-managed
service-linked role (AWSServiceRoleForConfig) for resource recording.

Root cause:
Config is enabled and recording (set up via Terraform in prior project).
However it uses a manually created IAM role, not the AWS service-linked
role. Security Hub flags this as non-compliant with CIS 2.5.

False positive? Partial. Config is running. The issue is the role type,
not Config being absent.

Risk:
Low in practice. Manually created roles can have misconfigured
permissions over time. Service-linked roles are AWS-managed and
automatically maintained.

Recommended action:
Update Config recorder to use AWSServiceRoleForConfig instead of the
manually created role. Can be done via Terraform or AWS console.

Analyst note:
Severity marked Critical by the standard but actual risk is low given
Config is actively recording. Priority is lower than the CloudTrail
and VPC findings in real-world triage.

## Finding 4: SSM.7 — SSM Block Public Sharing Not Enabled
Severity: Critical
Standard: AWS Foundational Security Best Practices v1.0.0
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
Account-level SSM setting must block public sharing of
Systems Manager documents.

Root cause:
Default AWS account state — block public sharing setting was never
explicitly enabled. No SSM documents exist in this account currently.

False positive? No. Genuine account-level misconfiguration.

Risk:
Low currently — no SSM documents exist to be exposed.
In a production account with SSM automation documents, this setting
being off could lead to accidental public exposure of internal
runbooks or scripts containing sensitive configuration details.

Recommended action:
AWS Console → Systems Manager → Documents → Settings →
Enable block public sharing.
Single toggle, no infrastructure change required.

Analyst note:
Easy one-click fix. Should be remediated immediately in any account
regardless of whether SSM documents exist, as a preventive control.

## Finding 5: GuardDuty.1 — GuardDuty Not Enabled
Severity: High
Standard: AWS Foundational Security Best Practices v1.0.0
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
Amazon GuardDuty must be enabled in the account and region.

Root cause:
GuardDuty was not enabled. Deliberate decision — lab account,
cost management.

False positive? Yes — from a lab context perspective.
GuardDuty is a paid service. Not enabling it is an intentional
cost decision, not a security oversight.

Risk:
In production: High. No automated threat detection means malicious
activity (credential abuse, crypto mining, data exfiltration) would
go undetected without manual monitoring.
In this lab account: Low. No sensitive workloads running.

Recommended action:
In production — enable GuardDuty with 30-day free trial, then
evaluate cost vs risk. Typically non-negotiable in enterprise accounts.

Analyst note:
This is a capability gap flag, not a misconfiguration finding.
Security Hub flags any missing recommended AWS service.
In a real SOC triage, this would be escalated to the cloud security
team as an architectural decision, not remediated at analyst level.

## Finding 6: Inspector.3 — Lambda Code Scanning Not Enabled
Severity: High
Standard: AWS Foundational Security Best Practices v1.0.0
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
Amazon Inspector Lambda code scanning must be enabled to automatically
scan Lambda function code for software vulnerabilities.

Root cause:
Inspector Lambda code scanning not enabled. Deliberate decision —
lab account, cost management.

False positive? Yes — from a lab context perspective.
Paid service, intentionally not enabled.

Risk:
In production: Medium-High. Lambda functions with vulnerable
dependencies (outdated packages, known CVEs) would go undetected
without automated scanning. Attacker could exploit a known library
vulnerability inside a function.
In this lab account: Low. One Lambda function exists (compliance
pipeline) with minimal external dependencies.

Recommended action:
In production — enable Inspector and activate Lambda standard and
code scanning. Inspector integrates directly with Security Hub,
pushing findings automatically.

Analyst note:
Capability gap flag, same category as GuardDuty.1.
Not a misconfiguration. Would not be remediated at analyst level —
requires architectural/cost decision by cloud security team or
management.

## Finding 7: IAM.14 — Password Policy Does Not Require a Number
Severity: Medium
Standard: CIS AWS Foundations Benchmark v1.2.0 (Control 1.8)
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
Account-level IAM password policy must require at least one numeric
character in passwords.

Root cause:
No custom IAM password policy has been configured for this account.
AWS default password policy does not enforce number requirements.

False positive? No. Genuine misconfiguration.

Risk:
Medium. Weak password policies allow users to set simple passwords,
increasing risk of brute force or credential stuffing attacks.
In a multi-user production account this is a meaningful control gap.
In a single-user lab account the practical risk is low.

Recommended action:
IAM Console → Account settings → Edit password policy →
Enable "Require at least one number."

Analyst note:
This finding and IAM.15 (minimum length) and IAM.16 (symbol
requirement) are all the same root cause — no custom password
policy set. All three can be remediated in a single action by
configuring a compliant password policy covering all requirements
at once.

## Finding 8: IAM.15 — Password Policy Minimum Length Below 14
Severity: Medium
Standard: CIS AWS Foundations Benchmark v1.2.0 (Control 1.9)
Status: FAIL
Resource: AWS Account 501578625134

What it checks:
Account-level IAM password policy must enforce a minimum password
length of 14 characters.

Root cause:
Same as IAM.14 — no custom IAM password policy configured.
AWS default does not enforce minimum length of 14.

False positive? No. Genuine misconfiguration.

Risk:
Medium. Short passwords are significantly easier to brute force.
14-character minimum is the CIS benchmark standard for enterprise
accounts. Single-user lab account — practical risk low.

Recommended action:
Same remediation as IAM.14 — configure a single compliant password
policy covering minimum length (14), number requirement, and symbol
requirement simultaneously.

Analyst note:
Grouped with IAM.14 and IAM.16 — all three are the same root cause
and same fix. In a real triage report these would be consolidated
into one remediation ticket.
