# Part 3: Node Configuration

Before installing Kubernetes, each node (VM) needs to be properly configured. These steps ensure 
that the nodes can communicate with each other, have system-level settings that are compatible 
with Kubernetes, and that the required kernel modules and networking rules are in place.

> **Important:** All steps in this section must be completed on every VM (controlplane-1, 
> controlplane-2, worker-1, worker-2).

---

## Steps

### A) Set the Hostname

Kubernetes uses hostnames to identify nodes. Each node must have a unique hostname.
```bash
sudo hostnamectl set-hostname <HostName>
```

For example, on your first control plane node:
```bash
sudo hostnamectl set-hostname controlplane-1
```

Repeat this on each VM using its corresponding name (controlplane-2, worker-1, worker-2).

---

### B) Disable Swap

Kubernetes requires swap to be disabled. If swap is active, there is a chance kubeadm will not initialize because swap interferes with Kubernetes' ability to accurately track and manage resources.

Disable swap:
```bash
sudo swapoff -a
```

Make the change persistent by commenting out any line containing the word `swap` in `/etc/fstab` (Not all systems have this entry in the /etc/fstab file):
```bash
sudo vi /etc/fstab
# Add a # to the beginning of any line containing the word swap
```

Verify swap is fully disabled:
```bash
free -h
# Swap line should be set to 0
```

> **Note:** Fedora enables zram (compressed RAM-based swap) by default. If zram is active 
> it will re-enable swap after reboot, so it must be removed.

Check if zram is active:
```bash
swapon --show
```

If zram is listed, remove it with the commands below:
```bash
# Find which zram package is installed
rpm -qa | grep zram

# Remove all listed packages
sudo dnf remove -y <PackageNames>

# Reboot the VM
sudo reboot
```

Repeat this process on all VMs.

---

### C) Set SELinux to Permissive

If SELinux is in enforcing mode, it can block Kubernetes and container runtime operations by 
restricting how processes interact with the filesystem and network. Permissive mode 
allows these operations but continues to log violations. This is the better approach as opposed to disabling SELinux entirely.

Set SELinux to permissive:
```bash
sudo setenforce 0
```

Make the change persistent by editing `/etc/selinux/config` and setting:
```
SELINUX=permissive
```

Verify the change took effect:
```bash
sestatus
# The output should show: Current mode: permissive
```
Repeat this process on all VMs.

---

### D) Check for Updates

Ensures you are starting with the latest packages so Kubernetes components do not have 
compatibility issues.
```bash
sudo dnf check-update
sudo dnf upgrade
```
Repeat this process on all VMs.

---

### E) Install Networking Tools

Install conntrack and tc. These tools are required for Kubernetes to manage connections and control traffic between pods and services.
```bash
sudo dnf install -y conntrack tc
```
Repeat this process on all VMs.

---

### F) Verify Network Connectivity Between Nodes

All the VMs must be able to communicate with each other, as well as with the bastion host. Your VMs should be on the same network as the bastion host.

**1) Note the IP address of each machine:**

Run the following on the bastion host and on each VM to get their IPs:
```bash
ip addr
```

**2) From the bastion host, ping each VM:**
```bash
ping <controlplane-1-IP>
ping <controlplane-2-IP>
ping <worker-1-IP>
ping <worker-2-IP>
```

**3) Login to each VM and ping all other VMs and the bastion host:**

Log into each VM and verify it can reach every other node and the bastion host.

---

### G) Configure Firewall Rules

Open the required ports on each node type to allow Kubernetes related traffic.

> **Reference:** See [docs/firewall-rules.md](../docs/firewall-rules.md) for a consolidated view of all firewall rules by host type.

**On the control plane nodes (VMs):**
```bash
# Kubernetes API server
sudo firewall-cmd --permanent --add-port=6443/tcp
# etcd
sudo firewall-cmd --permanent --add-port=2379-2380/tcp
# Kubelet API
sudo firewall-cmd --permanent --add-port=10250/tcp
# kube-scheduler
sudo firewall-cmd --permanent --add-port=10251/tcp
# kube-controller-manager
sudo firewall-cmd --permanent --add-port=10252/tcp
# Enable pod network traffic
sudo firewall-cmd --permanent --add-masquerade

sudo firewall-cmd --reload
```

**On the worker nodes (VMs):**
```bash
# Kubelet API
sudo firewall-cmd --permanent --add-port=10250/tcp
# NodePort Services
sudo firewall-cmd --permanent --add-port=30000-32767/tcp
# Enable pod network traffic
sudo firewall-cmd --permanent --add-masquerade

sudo firewall-cmd --reload
```


---

### H) Enable IP Forwarding

By default, Linux does not forward network packets between interfaces. Kubernetes nodes 
need to route traffic between pods, services, and the outside world. Without IP forwarding 
enabled, pod-to-pod and pod-to-service communication will not work.
```bash
sudo sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/99-kubernetes.conf
sudo sysctl -p /etc/sysctl.d/99-kubernetes.conf
```
Repeat this process on all VMs.

---

### I) Load Required Kernel Modules

Two kernel modules are required for Kubernetes to function correctly:

- **overlay** — containerd uses overlay filesystems to layer container images. Without it containers cannot start.
- **br_netfilter** — allows iptables and nftables to see and filter traffic across the virtual network bridge that Kubernetes uses for pod networking. Without it, kube-proxy and network policies cannot manage pod traffic.

Load the modules for overlay and br_netfilter:
```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

Make the modules load automatically on reboot:
```bash
cat <<EOF | sudo tee /etc/modules-load.d/kubernetes.conf
overlay
br_netfilter
EOF
```

Add bridge networking sysctl settings and reload all kernel parameters without requiring a reboot:
```bash
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes.conf
net.ipv4.ip_forward=1
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
EOF

sudo sysctl --system
```

---

> **Important:** Confirm that steps A through I have been completed on all four VMs before 
> proceeding to Part 4.

---

> **Next:** [Part 4: Containerd Setup](../Part%204:%20Containerd%20Setup/)
>
> **Previous:** [Part 2: VM Provisioning](../Part%202:%20VM%20Provisioning/)
