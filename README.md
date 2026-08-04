# SOC Analyst Incident Response Lab

A hands-on home lab built to practice real SOC analyst workflows: log collection,
SIEM detection engineering, threat hunting, and incident documentation.

## Goal

Move beyond theory and build practical, demonstrable skills:
- Deploying and tuning a SIEM (Wazuh)
- Onboarding log sources (Linux, later Windows)
- Writing and validating custom detection rules
- Simulating attacks and hunting for them in logs
- Writing incident response reports the way a real SOC would

## Architecture

See [docs/architecture.md](docs/architecture.md) for the full network diagram and
explanation of each component.

**Summary:**
- **Hypervisor:** KVM/QEMU (native Linux virtualization)
- **SIEM:** Wazuh (manager + indexer + dashboard), deployed via Docker
- **Victim endpoint(s):** Ubuntu Server VM (Windows added in phase 2)
- **Attacker box:** Kali Linux VM (phase 2)

## Build Log

Every step, decision, and problem encountered is documented in
[docs/setup-log.md](docs/setup-log.md) — treat it like an analyst's shift notes.

## Detections

Custom detection rules I've written, with the reasoning behind each one, live in
[detections/](detections/).

## Simulated Incidents

Write-ups of simulated attacks I detected and responded to live in
[incidents/](incidents/) — each includes timeline, IOCs, and response actions,
formatted like a real IR report.

## Status

🚧 Phase 1 in progress: SIEM + log analysis foundation.

## Disclaimer

This lab is fully isolated (host-only/internal virtual networks) and used solely
for defensive security education. No attacks are directed at systems outside this
lab's isolated network.
