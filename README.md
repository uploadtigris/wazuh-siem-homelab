# Wazuh SIEM Homelab

A self-hosted Wazuh SIEM for my home network. I ran it once with agents on my
Linux and macOS machines. I'm rebuilding it on a dedicated laptop so it collects
logs from the network itself: pfSense firewall logs and Pi-hole DNS queries.

**Status labels:** ![running](https://img.shields.io/badge/running-2E7D32) is up and working now,
![in progress](https://img.shields.io/badge/in_progress-F9A825) is being built,
![planned](https://img.shields.io/badge/planned-757575) is designed but not started.

> The first build **no longer runs**. The rebuild is planned for late October 2026,
> on top of my five-VLAN segmented network
> ([network-segmentation-ids](https://github.com/uploadtigris/network-segmentation-ids)).

## First build (no longer running)

- Deployed the Wazuh stack (indexer, manager, dashboard) with Docker Compose.
- Enrolled Wazuh agents on Linux (Ubuntu) and macOS with file integrity
  monitoring and security configuration assessment (SCA) policies.
- Ran Wazuh's CIS benchmark checks on two Ubuntu hosts. The first scan
  showed about 100 failed checks per host. That is the baseline the rebuild
  starts from.
- Used Wazuh's built-in rules, which tag alerts with MITRE ATT&CK techniques.

## The rebuild ![planned](https://img.shields.io/badge/planned-757575)

Where it will run: a Dell Latitude 5420 (Ubuntu, 32 GB RAM) in the Servers VLAN,
as a single-node Wazuh install in Docker.

What it will collect:

| Source | How it gets to Wazuh | Why |
|---|---|---|
| pfSense firewall | Remote syslog, UDP 514 | See blocked and allowed traffic between VLANs |
| Pi-hole (Raspberry Pi 2) | Wazuh agent, or rsyslog if the agent doesn't support the Pi's 32-bit ARM | See DNS queries from every device, including IoT |
| The Wazuh server itself | Wazuh agent | File integrity and CIS checks on the server |
| My laptop | Wazuh agent | File integrity and CIS checks on a daily-use machine |

## Roadmap

- [x] Wazuh stack deployed with Docker Compose (first build, no longer running)
- [x] Linux and macOS agents enrolled with file integrity monitoring and SCA
- [x] CIS baseline captured: about 100 failed checks per Ubuntu host
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Rebuild single-node Wazuh on the Latitude 5420
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Agents on the server and my laptop
- [ ] ![planned](https://img.shields.io/badge/planned-757575) pfSense remote syslog into Wazuh
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Pi-hole logs into Wazuh (agent or rsyslog, with the reason written down)
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Sort failed CIS checks into fix now, fix with a script, or accept with a reason
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Fix the first batch and re-scan; record the before and after scores
- [ ] ![planned](https://img.shields.io/badge/planned-757575) A short tuning log: which alerts I keep, which I silence, and why
- [ ] ![planned](https://img.shields.io/badge/planned-757575) Later: alerts from a Suricata sensor on the segmented network

## Stack

Wazuh · Docker Compose · Ubuntu · macOS · pfSense syslog · Pi-hole · CIS Benchmarks · MITRE ATT&CK
