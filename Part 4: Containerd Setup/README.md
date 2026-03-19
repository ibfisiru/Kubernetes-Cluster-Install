# Part 4: Containerd Setup

Containerd is the container runtime for the cluster. It manages the lifecycle of containers 
on each node and handles tasks like as starting, stopping, and deleting containers. Containerd 
also handles image pulls and pushes, storage, and network integration.

> **Important:** All steps in this section must be completed on every VM (controlplane-1, 
> controlplane-2, worker-1, worker-2).

---

## Steps

### A) Install containerd
```bash
sudo dnf install -y containerd
```

---

### B) Generate a default containerd configuration

Containerd requires a configuration file to know how to run. Generate the default config:
```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

---

### C) Set systemd as the Cgroup Driver

Both kubernetes and containerd must use the same cgroup driver. Cgroups (control groups) organize 
processes into groups to manage and limit their consumption of system resources like CPU, 
memory, and disk I/O. Systemd is the correct cgroup driver for this setup. Mismatched cgroup 
drivers can cause the cluster to fail.
```bash
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```

---

### D) Enable and Start Containerd
```bash
sudo systemctl enable --now containerd
sudo systemctl status containerd
```

---

> **Important:** Confirm that steps A through D have been completed on all four VMs before 
> proceeding to Part 5.

---

> **Next:** [Part 5: Kubernetes Components](../Part%205:%20Kubernetes%20Components/)
