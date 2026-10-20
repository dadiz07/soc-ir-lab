# Rebuild Runbook

Everything here was done on Parrot OS (Debian-based, dual boot). Needs ~15GB free disk.

## 1. Virtualization
    sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager acl
    sudo usermod -aG libvirt,kvm $USER
    sudo systemctl enable --now libvirtd
    sudo virsh net-start default && sudo virsh net-autostart default
    echo 'export LIBVIRT_DEFAULT_URI="qemu:///system"' >> ~/.bashrc

## 2. Docker Engine (official repo, not the podman-docker shim)
See setup-log.md for the podman conflict and the leftover DOCKER_HOST fix.

## 3. Wazuh (single node)
    sudo sysctl -w vm.max_map_count=262144
    git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.0
    cd wazuh-docker/single-node
    docker compose -f generate-indexer-certs.yml run --rm generator
    docker compose up -d
Dashboard: https://localhost (self-signed cert warning is expected).

## 4. Ubuntu victim VM (cloud image + cloud-init, thin-provisioned)
    cd ~/vms
    wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
    qemu-img create -f qcow2 -F qcow2 -b noble-server-cloudimg-amd64.img ubuntu-victim.qcow2 8G
    mkpasswd --method=SHA-512 --rounds=4096      # put the hash in user-data
    cloud-localds seed.iso user-data meta-data
    setfacl -m u:libvirt-qemu:--x /home/$USER /home/$USER/vms
    virt-install --name soc-victim-01 --memory 2048 --vcpus 2 \
      --disk path=$HOME/vms/ubuntu-victim.qcow2,format=qcow2 \
      --disk path=$HOME/vms/seed.iso,device=cdrom \
      --os-variant ubuntu24.04 --network network=default,model=virtio \
      --graphics none --import --noautoconsole

## 5. Next: install the Wazuh agent on the VM
Manager address from the VM's point of view: 192.168.122.1 (virbr0 gateway).
