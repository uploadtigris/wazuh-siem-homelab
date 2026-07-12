# Planning Phase — Initial Goals

## Stated goals (as of 2026-07-06)

1. Get Docker installed on the Fedora homelab server (not the macOS laptop —
   the laptop is a Wazuh agent/client only, it does not run Docker).
2. Open the correct port(s) so the Wazuh manager (running in Docker on the
   Fedora server) can communicate with the macOS laptop as an agent (user
   initially assumed "the HTTPS port" — see note below).
3. Install Wazuh (manager/server components, via Docker Compose) on the
   Fedora homelab server.
4. Install the Wazuh agent on the macOS laptop, enrolled against the
   Fedora server's Wazuh manager.

## Note on ports (to verify while implementing)

Wazuh agent-to-manager traffic is not HTTPS/443. The ports that need to be
reachable *from the laptop to the Fedora server* are:

- **1514/tcp** — agent → manager event/data channel (encrypted).
- **1515/tcp** — agent enrollment (registering a new agent with the manager).

443/tcp (HTTPS) is used separately, on the server side, for the Wazuh
dashboard UI and the Wazuh API (default 55000/tcp for the API) — those are
for humans/browsers hitting the server, not for the laptop-as-agent traffic.
Since the laptop is the client here, no inbound port needs to be opened on
the laptop itself; the laptop needs outbound access to 1514/1515 on the
Fedora server, and the Fedora server (via `firewalld`, since it's Fedora)
needs those ports open inbound from the laptop/LAN.

To confirm/adjust once the manager is actually deployed (exact ports can be
customized in `ossec.conf` / the Wazuh Docker Compose files).

## Out of scope for this record

Server sizing, Docker Compose topology (single-node vs. multi-node), and
Ansible playbook structure are not decided yet — to be captured in a later
planning record once those decisions are made.
