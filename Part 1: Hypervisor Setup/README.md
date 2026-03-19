# Part 1: Hypervisor Setup

QEMU and libvirt are the virtualization stack that will host the Kubernetes cluster nodes as 
virtual machines on the bastion host. QEMU is the emulator that actually runs the VMs and libvirt is 
the management layer that sits on top of QEMU, providing the tooling needed to create, configure, and 
control the VM's. Virtual Machine Manager (virt-manager) is the GUI that connects to libvirt.

---

## Steps

### A) Install QEMU and libvirt

Use the official Fedora documentation to install both QEMU and libvirt on the bastion host (Your Fedora Workstation):

[Installing libvirt and virt-install on Fedora](https://developer.fedoraproject.org/tools/virtualization/installing-libvirt-and-virt-install-on-fedora-linux.html)

---

### B) Enable and start libvirt

Once installed, enable and start the libvirt daemon:
```bash
sudo systemctl enable --now libvirtd
```

Verify that the libvirt daemon is running:
```bash
sudo systemctl status libvirtd
```

---

### C) Connect QEMU in Virtual Machine Manager

Open **Virtual Machine Manager** from your application menu. In the UI, ensure QEMU is 
connected as a hypervisor. QEMU must be connected before you can create VMs.

---

> **Next:** [Part 2: VM Provisioning](../Part%202:%20VM%20Provisioning/)
