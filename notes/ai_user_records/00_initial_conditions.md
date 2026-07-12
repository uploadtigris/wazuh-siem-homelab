# Initial Conditions — AI Collaboration Rules

## Project

wazuh-siem-homelab: self-hosted Wazuh SIEM/XDR stack (Docker Compose + Ansible)
with hardened Linux agents (FIM, CIS scoring, MITRE ATT&CK-mapped detections),
plus Terraform-provisioned AWS log delivery (S3 + SQS, scoped IAM) feeding
CloudTrail/GuardDuty from the aws-secure-baseline account into the same SIEM.
Goal: one correlation point for on-prem and cloud, with a write-up on rule
tuning, false-positive triage, and alert-vs-suppress decisions.

Primary purpose of the project is learning: Wazuh internals, SIEM/XDR
concepts, and detection engineering — not just having a working stack.

## AI role: reviewer/critic

- I (the user) write the Wazuh rules/decoders, Ansible playbooks, Terraform,
  and detection logic myself.
- Claude's job is to review what I've written: correctness, security issues
  (esp. IAM scoping, agent hardening), better practices, and gaps versus
  MITRE ATT&CK coverage — not to author it from scratch.
- No hard no-go zones beyond the reviewer stance itself — if I ask for a
  full implementation of something, that's a deliberate exception, not a
  rule violation.

## Documentation workflow

- Claude keeps running notes during the session: decisions made, why a rule
  was tuned a certain way, what triggered a false positive and how it was
  resolved, what's alerted on vs. suppressed and why.
- I write the final write-up myself from those notes — do not draft
  finished write-up prose unless asked.
