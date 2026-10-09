# Wazuh SIEM Homelab

A self-hosted Wazuh SIEM for my home network. I ran it once with agents on my
Linux and macOS machines; I'm rebuilding it on my home server so it collects
logs from the network itself: pfSense firewall logs and Pi-hole DNS queries.

**Status means what it says:** 

![done](https://img.shields.io/badge/done-2E7D32) was built and worked,

![planned](https://img.shields.io/badge/planned-757575) is designed but not started.

> The first build **no longer runs**. The rebuild is planned for late October 2026,
> now that my VLAN segmentation is finished
> ([network-segmentation-ids](https://github.com/uploadtigris/network-segmentation-ids)).

## What I built before ![done](https://img.shields.io/badge/done-2E7D32)

- Deployed the Wazuh stack (indexer, manager, dashboard) with Docker Compose.
- Enrolled Wazuh agents on Linux (Ubuntu) and macOS with file integrity
  monitoring and security configuration assessment (SCA) policies.
- Ran Wazuh's CIS benchmark checks on two Ubuntu hosts. The first scan
  showed about 100 failed checks per host. That is the baseline the rebuild
  starts from.
- Used Wazuh's built-in rules, which tag alerts with MITRE ATT&CK techniques.

## The rebuild ![planned](https://img.shields.io/badge/planned-757575)

Where it runs: a Dell Latitude 7490 (Ubuntu, 16 GB RAM) that moves to the Servers VLAN
(`10.0.50.0/24`) when NextCloud is set up, as a single-node Wazuh install in Docker with the indexer's
Java heap capped at about 2 GB so it shares the box with my other tools.

What it collects:

| Source | How it gets to Wazuh | Why |
|---|---|---|
| pfSense firewall | Remote syslog, UDP 514 | See blocked and allowed traffic between VLANs |
| Pi-hole (Raspberry Pi 2) | Wazuh agent, or rsyslog if the agent doesn't support the Pi's 32-bit ARM | See DNS queries from every device, including IoT |
| Latitude (the server itself) | Wazuh agent | File integrity and CIS checks on the server |
| My laptop | Wazuh agent | File integrity and CIS checks on a daily-use machine |

## What's in this repo

| Path | What it holds |
|---|---|
| `README.md` | What was built, the rebuild plan, the roadmap |
| [`docs/build-log.md`](docs/build-log.md) | Step-by-step checklist, verification matrix and problems hit for the rebuild |
| [`docs/archive-2026-07/`](docs/archive-2026-07/) | Notes from the first build in July 2026 (no longer runs, kept for the record) |
| [`images/`](images/) | Screenshots |

The rebuild is logged in `docs/build-log.md` as it happens.

## Roadmap

- [x] Wazuh stack deployed with Docker Compose (first build, no longer running)
- [x] Linux and macOS agents enrolled with file integrity monitoring and SCA
- [x] CIS baseline captured: about 100 failed checks per Ubuntu host
- [ ] Rebuild single-node Wazuh on the Latitude with a capped indexer heap
- [ ] Agents on the Latitude and my laptop
- [ ] pfSense remote syslog into Wazuh
- [ ] Pi-hole logs into Wazuh (agent or rsyslog, with the reason written down)
- [ ] Sort failed CIS checks into fix now, fix with a script, or accept with a reason
- [ ] Fix the first batch and re-scan; record the before and after scores
- [ ] A short tuning log: which alerts I keep, which I silence, and why
- [ ] Later: alerts from a Suricata sensor on the segmented network

## Stack

Wazuh · Docker Compose · Ubuntu · macOS · pfSense syslog · Pi-hole · CIS Benchmarks · MITRE ATT&CK
