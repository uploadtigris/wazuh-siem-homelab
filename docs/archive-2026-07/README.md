# Archive: first build, July 2026

These notes describe my first Wazuh build. They are kept for the record and do
**not** describe the current plan.

- The first build no longer runs.
- The notes were written while building, so they are rough. `03_importing-suricata-data.md`
  is a stub that was never finished.
- Detection from a Suricata sensor is not part of the rebuild. It comes later, after
  the segmentation build ([network-segmentation-ids](https://github.com/uploadtigris/network-segmentation-ids)).

The current plan is in the [README](../../README.md) and the current work is in
the [build log](../build-log.md).

| File | What it covers |
|---|---|
| [01_installing-docker.md](01_installing-docker.md) | Host firewall ports and agent enrollment |
| [02_configuring-wazuh.md](02_configuring-wazuh.md) | Wazuh in Docker Compose |
| [03_importing-suricata-data.md](03_importing-suricata-data.md) | A stub, not finished |
