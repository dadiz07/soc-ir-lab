# Lab Architecture

## Overview

This lab uses an isolated internal virtual network so that all traffic (log
generation, later attack simulation) stays contained and never touches
production networks or the public internet.

## Components

### Hypervisor: KVM/QEMU
Chosen over VirtualBox because it's native to Linux (no additional kernel
modules fighting with the host), gives near-bare-metal performance since this
lab runs on bare-metal dual-boot Parrot OS, and is what a lot of real infra
teams use under the hood (proxmox, cloud providers, etc).

### SIEM: Wazuh
Chosen because it's free/open-source, has a large library of default detection
rules to learn from, supports both Linux and Windows agents, and mirrors real
SOC tooling patterns (manager, indexer, dashboard, agents) closely enough to be
resume-relevant.

### Network Diagram

(Add a diagram here once the lab network is live — e.g. draw.io export or ASCII)

```
[ Ubuntu Victim VM ] --agent--> [ Wazuh Manager/Indexer/Dashboard (Docker) ]
        |
   (internal virtual network, host-only, no internet bridge to attacker box)
```

### Why isolation matters
Every VM in this lab sits on an internal-only virtual network (not bridged to
your home network). This is standard practice for any lab that will later run
attack simulations — it guarantees nothing "escapes" to affect other devices.
