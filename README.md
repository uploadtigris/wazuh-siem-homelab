# wazuh-siem-homelab

A self-hosted Wazuh SIEM/XDR stack, deployed with Docker Compose and configured through Ansible. Agents on hardened Linux hosts provide file integrity monitoring, CIS security configuration assessment, and detection rules mapped to MITRE ATT&CK. Terraform provisions the AWS-side log delivery (S3 + SQS with scoped IAM) so the same SIEM ingests CloudTrail and GuardDuty from the aws-secure-baseline account. one correlation point for on-prem and cloud. The write-up covers rule tuning, false-positive triage, and what I alert on versus suppress.
