# Wazuh Rebuild: Build Log

> [!IMPORTANT]
> The subnets and device IP addresses on this page are for demonstration only. They
> are not the ones I actually use. I keep the real addressing out of this repo for
> security reasons.

**Date:** 2026-10-24 (planned) to 2026-10-__  
**OS:** Ubuntu (Latitude 5420), Raspberry Pi OS, pfSense, macOS  
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

Rebuild Wazuh as a single node on a Dell Latitude 5420 (32 GB) so it collects logs from the
network itself, and prove each source reaches the dashboard. The 5420 sits on switch port 7,
in the Servers VLAN, at 10.0.50.30 in this demo scheme.

| Source | Where it runs | How it reaches Wazuh | Why |
|---|---|---|---|
| pfSense firewall | Router | Remote syslog, UDP 514 | Blocked and allowed traffic between VLANs |
| Pi-hole | Raspberry Pi 2 | Agent, or rsyslog (reason written down) | DNS queries from every device |
| Latitude 5420 (the Wazuh server) | Servers VLAN, switch port 7 | Wazuh agent | File integrity and CIS checks on the server |
| Laptop | Trusted VLAN | Wazuh agent (the one Trusted agent) | File integrity and CIS checks on a daily-use machine |

Done when: every row of the verification matrix below passes, or the failure is documented.

## Action

Tick each item when it is done and the evidence is saved. Add the date to each step.

### 0. Before touching anything — Date: ____

- [ ] Segmentation build finished and the test matrix passed
- [ ] The 5420 is on switch port 7, which moves from the Trusted spare to Servers: untagged in VLAN 50, PVID 50, excluded from VLAN 1
- [ ] The 5420 has a static Servers address (10.0.50.30 in this demo scheme)
- [ ] Free disk space and RAM on the 5420 checked and written down
- [ ] Config backups of pfSense and the Pi-hole taken
- [ ] Old first-build notes moved to `archive-2026-07/` (done)

### 0.5 pfSense: let the Wazuh server reach the internet — Date: ____

_Why: the only Servers pass rule today is the Pi-hole's, so a second server on Servers would have no internet to pull Docker images or updates. Its DNS goes to the Pi-hole on the same VLAN, so DNS needs no rule._

| Interface | Action | Source | Destination | Port | Why |
|---|---|---|---|---|---|
| Servers | Pass | `WAZUH_SERVER` | any | TCP 80 and 443, UDP 123 | Image pulls, updates and time. It sits below the Servers blocks, because first match wins |

- [ ] Create the alias `WAZUH_SERVER` (the 5420's Servers address)
- [ ] Add the rule below the two Servers block rules
- [ ] Verify from the 5420: HTTPS to the internet works, and a connection to another VLAN times out
- [ ] Evidence: the rule, and the two test results

### 1. Wazuh single node on the Latitude 5420 — Date: ____

- [ ] Docker and Docker Compose confirmed working
- [ ] Single-node Wazuh stack deployed (indexer, manager, dashboard)
- [ ] Dashboard published on host port 8443 (443 stays free for NextCloud), reached from the laptop only (see step 1.5)
- [ ] Evidence: screenshot of the running containers and the dashboard

```
# notes, commands, output
```

**Screenshots**

_Placeholder: Running containers_ — the three Wazuh containers up and healthy  
<!-- ![Running containers](../images/01_containers.png) -->

_Placeholder: Dashboard_ — the Wazuh dashboard loaded from the laptop on TCP 8443  
<!-- ![Dashboard](../images/01_dashboard.png) -->

### 1.5 pfSense: one narrow Trusted exception for Wazuh — Date: ____

_Why: the Trusted rules block every internal network, so nothing on Trusted can reach the Wazuh server (the 5420). The laptop is the one Trusted device that needs it: it is the one Trusted agent and the only dashboard client. I'm opening one narrow rule for it, not wired Mgmt access and not a general Trusted-to-Servers path._

| Interface | Action | Source | Destination | Port | Why |
|---|---|---|---|---|---|
| Trusted | Pass | `WAZUH_LAPTOP` | The 5420 (10.0.50.30) | TCP `WAZUH_PORTS` (1514, 1515, 8443) | The laptop is the one Trusted agent (1514, 1515) and the only dashboard client (8443). It sits above the `PRIVATE_NETS` block, because first match wins |

Everything else from Trusted to the 5420 stays blocked.

- [ ] Give the laptop a DHCP static mapping on the Trusted VLAN, if it doesn't have one
- [ ] Create the alias `WAZUH_LAPTOP` (the laptop's Trusted address) and the port alias `WAZUH_PORTS` (1514, 1515, 8443)
- [ ] Add the rule above, above the Trusted `PRIVATE_NETS` block
- [ ] Verify from the laptop: the dashboard loads on 8443, and other ports on the 5420 (443, SSH) still time out
- [ ] Verify from another Trusted device: the dashboard and the agent ports time out
- [ ] Evidence: the rule, and the two test results

### 2. Host firewall and agent ports — Date: ____

- [ ] List open ports on the 5420 and note why each is open
- [ ] Allow agent traffic only from the machines that need it: the laptop (through the rule in step 1.5), the Pi-hole and pfSense
- [ ] Confirm the agent ports are not reachable from IoT, Guest or any Trusted device except the laptop
- [ ] Evidence: the firewall rules and the open-port list

```
# notes, commands, output
```

### 3. Agents: the Wazuh server and laptop — Date: ____

- [ ] Agent enrolled on the Wazuh server (the 5420)
- [ ] Agent enrolled on the laptop (the one Trusted agent)
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

- [ ] Run the CIS scan on the Wazuh server and the laptop; record the starting score
- [ ] Sort every failed check: fix now, fix with a script, or accept with a reason
- [ ] Fix the first batch
- [ ] Re-scan and record the before and after scores
- [ ] Evidence: before and after screenshots

| Host | Before | After | Fixed | Accepted (with reason) |
|---|---|---|---|---|
| Wazuh server (5420) | | | | |
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
| 1 | Agents report in | Wazuh server, laptop | Check the agents list | Both active | | ☐ |
| 2 | File integrity alert | Wazuh server | Change a monitored file | Alert in the dashboard | | ☐ |
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
