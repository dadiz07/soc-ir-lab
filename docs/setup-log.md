# Setup Log

Running journal of the build. Format: date, what I did, why, what broke, what I learned.
This becomes your proof-of-work for interviews — keep it honest, including mistakes.

---

## YYYY-MM-DD — Phase 1: Repo scaffolding

- Created repo structure (siem/, detections/, incidents/, docs/)
- Why: separates infrastructure config, detection engineering, and incident
  documentation the way a real SOC team would organize a case management system

---

## YYYY-MM-DD — Installed KVM/QEMU + virt-manager

- (fill in what you ran, any errors, how you fixed them)

---

## YYYY-MM-DD — Deployed Wazuh via Docker

- (fill in)

---

---

## Phase 1 summary: what I built and what broke

Built: KVM/libvirt, Docker Engine, Wazuh 4.9.0 single-node (manager, indexer, dashboard), Ubuntu 24.04 victim VM on libvirt NAT (192.168.122.0/24).

### Issues and fixes (diagnose, root cause, fix, verify)
1. **docker run failed, pointed at a podman socket.** Cause: the podman-docker package left /etc/profile.d/podman-docker.sh exporting DOCKER_HOST. Fix: removed the leftover scripts, unset DOCKER_HOST, verified with hello-world.
2. **Wazuh cert generator failed on first run.** Cause: transient network failure while the container image was still pulling. Verified container-to-internet with a curl container, re-ran successfully.
3. **Disk full (49GB root shared with /home).** Cleaned junk, then switched from a 3GB server ISO to a ~600MB Ubuntu cloud image with a thin-provisioned qcow2 overlay (196KB at creation, 8GB virtual) provisioned by cloud-init.
4. **virt-install: network 'default' not found.** Cause: virsh was using qemu:///session instead of qemu:///system. Fix: started and autostarted the default network, set LIBVIRT_DEFAULT_URI.
5. **QEMU permission denied on the disk.** Cause: libvirt-qemu user could not traverse /home. Fix: minimal ACL (--x) on the two directories.
6. **SSH: Permission denied (publickey).** Cause: Ubuntu cloud images ship 60-cloudimg-settings.conf with PasswordAuthentication no, which overrides cloud-init. Fix: edited the drop-in.
7. **Locked out of the VM (forgot password).** Recovered offline with libguestfs/guestfish: mounted the qcow2 with the VM powered off and reset the hash with usermod. Same technique used in real disk forensics.

### Lessons
- Read the exact error, then trace where the config actually comes from.
- Plan disk budget before building a lab.
- Never commit credentials or password hashes.

### Status
Paused at: VM running and reachable, Wazuh stack healthy. Next: Wazuh agent on the VM, first custom detection rule, then Kali attack simulation.
