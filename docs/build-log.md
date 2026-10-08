# Wazuh Rebuild: Build Log

> [!IMPORTANT]
> The subnets and device IP addresses on this page are for demonstration only. They
> are not the ones I actually use. I keep the real addressing out of this repo for
> security reasons.

**Date:** 2026-10-__ to 2026-10-__  
**OS:** Ubuntu (Latitude), Raspberry Pi OS, pfSense, macOS  
**Environment:** Homelab  
**Category:** SIEM, Logging, Detection, Hardening  
**Status:** Planned (starts after the segmentation build is finished)

The plan is in the [README](../README.md). This page is the working log: what I
did, in order, and what broke along the way. Screenshots live in [`../images/`](../images/).
Notes from the first build are in [`archive-2026-07/`](archive-2026-07/).

---

## Situation

_What did the first build do, and why rebuild it?_

- What the first build collected, and where it ran:
- Why it no longer runs:
- What prompted the rebuild (network logs, not just host logs):

## Task

Rebuild Wazuh as a single node on the Latitude so it collects logs from the network
itself, and prove each source reaches the dashboard.

| Source | Where it runs | How it reaches Wazuh | Why |
|---|---|---|---|
| pfSense firewall | Router | Remote syslog, UDP 514 | Blocked and allowed traffic between VLANs |
| Pi-hole | Raspberry Pi 2 | Agent, or rsyslog (reason written down) | DNS queries from every device |
| Latitude | Servers VLAN | Wazuh agent | File integrity and CIS checks on the server |
| Laptop | Trusted VLAN | Wazuh agent | File integrity and CIS checks on a daily-use machine |

Done when: every row of the verification matrix below passes, or the failure is documented.

## Action

Tick each item when it is done and the evidence is saved. Add the date to each step.

### 0. Before touching anything — Date: ____

- [ ] Segmentation build finished and the test matrix passed
- [ ] Latitude is on the Servers VLAN and reachable from Trusted
- [ ] Free disk space and RAM on the Latitude checked and written down
- [ ] Config backups of pfSense and the Pi-hole taken
- [ ] Old first-build notes moved to `archive-2026-07/` (done)

### 1. Wazuh single node on the Latitude — Date: ____

- [ ] Docker and Docker Compose confirmed working
- [ ] Single-node Wazuh stack deployed (indexer, manager, dashboard)
- [ ] Indexer Java heap capped at about 2 GB
- [ ] Dashboard loads from the Trusted VLAN
- [ ] Evidence: screenshot of the running containers and the dashboard

```
# notes, commands, output
```

**Screenshots**

_Placeholder: Running containers_ — the three Wazuh containers up and healthy  
<!-- ![Running containers](../images/01_containers.png) -->

_Placeholder: Dashboard_ — the Wazuh dashboard loaded from the Trusted VLAN  
<!-- ![Dashboard](../images/01_dashboard.png) -->

### 2. Host firewall and agent ports — Date: ____

- [ ] List open ports on the Latitude and note why each is open
- [ ] Allow agent traffic only from the machines that need it
- [ ] Confirm the agent ports are not reachable from IoT or Guest
- [ ] Evidence: the firewall rules and the open-port list

```
# notes, commands, output
```

### 3. Agents: Latitude and laptop — Date: ____

- [ ] Agent enrolled on the Latitude
- [ ] Agent enrolled on the laptop
- [ ] Both agents show as active in the dashboard
- [ ] File integrity monitoring and SCA running on both
- [ ] Evidence: screenshot of the agents list

```
# notes, commands, output
```

**Screenshots**

_Placeholder: Agents list_ — both agents active  
<!-- ![Agents list](../images/03_agents.png) -->

### 4. pfSense logs into Wazuh — Date: ____

- [ ] Remote syslog on pfSense pointed at the Wazuh host (UDP 514)
- [ ] Wazuh set to receive and parse the syslog
- [ ] A blocked connection between VLANs shows up in the dashboard
- [ ] Evidence: screenshot of a pfSense event in Wazuh

```
# notes, commands, output
```

**Screenshots**

_Placeholder: pfSense event in Wazuh_ — a blocked inter-VLAN connection  
<!-- ![pfSense event in Wazuh](../images/04_pfsense_event.png) -->

### 5. Pi-hole logs into Wazuh — Date: ____

- [ ] Check whether the Wazuh agent supports the Pi's 32-bit ARM
- [ ] Choose agent or rsyslog, and write the reason down
- [ ] DNS queries from an IoT device show up in the dashboard
- [ ] Evidence: screenshot of a Pi-hole query in Wazuh

```
# notes, commands, output
```

**Screenshots**

_Placeholder: Pi-hole query in Wazuh_ — a DNS query from another VLAN  
<!-- ![Pi-hole query in Wazuh](../images/05_pihole_event.png) -->

### 6. CIS baseline: sort, fix, re-scan — Date: ____

- [ ] Run the CIS scan on the Latitude and the laptop; record the starting score
- [ ] Sort every failed check: fix now, fix with a script, or accept with a reason
- [ ] Fix the first batch
- [ ] Re-scan and record the before and after scores
- [ ] Evidence: before and after screenshots

| Host | Before | After | Fixed | Accepted (with reason) |
|---|---|---|---|---|
| Latitude | | | | |
| Laptop | | | | |

**Screenshots**

_Placeholder: CIS before_ — the first scan result  
<!-- ![CIS before](../images/06_cis_before.png) -->

_Placeholder: CIS after_ — the re-scan after the first batch of fixes  
<!-- ![CIS after](../images/06_cis_after.png) -->

### 7. Tuning log — Date: ____

Which alerts I keep, which I silence, and why. One row per decision.

| Date | Alert / rule | What triggered it | Decision (keep / silence / tune) | Why |
|---|---|---|---|---|
| | | | | |

### 8. Verification matrix — Date: ____

Screenshot every result.

| # | Test | From | What I do | Expected | Actual | Pass |
|---|---|---|---|---|---|:-:|
| 1 | Agents report in | Latitude, laptop | Check the agents list | Both active | | ☐ |
| 2 | File integrity alert | Latitude | Change a monitored file | Alert in the dashboard | | ☐ |
| 3 | pfSense block shows up | Guest or IoT device | Try a blocked connection | Event in Wazuh | | ☐ |
| 4 | Pi-hole queries show up | IoT device | Look up a domain | Query in Wazuh | | ☐ |
| 5 | Agent ports closed to other VLANs | Guest or IoT device | Try the agent port | Fails | | ☐ |

### 9. Write-up — Date: ____

- [ ] Roadmap in the README ticked off, only for what has evidence
- [ ] Network diagram updated to show the log flow
- [ ] "Problems I hit" section completed from the entries below
- [ ] README status changed from planned to done (only after the matrix passes)
- [ ] Portfolio site Wazuh card updated

## Result

_Fill in once the verification matrix has been run._

- Outcome:
- Verification matrix: __ of 5 passed
- What I would do differently:
- Lesson learned:

---

## Problems I hit

One STAR entry per problem. Copy the blank entry for each new one.

### Problem 1: [title]

**Date:** 2026-10-__

**Situation** —

**Task** —

**Action** —

```
# commands and output
```

**Result** —

### Problem 2: [title]

**Date:** 2026-10-__

**Situation** —

**Task** —

**Action** —

```
# commands and output
```

**Result** —

---

**Tags:** `wazuh` `siem` `docker` `pfsense` `syslog` `pihole` `cis-benchmarks` `mitre-attack` `homelab`
