# Part 5: Kubernetes Components

Here you install the three core Kubernetes components on every node. These are the tools that will 
be used to bootstrap, manage, and run the cluster.

- **kubeadm**: Bootstraps the cluster and manages the control plane configuration
- **kubelet**: Is the agent that runs on every node and manages containers on that node
- **kubectl**: Is the command line tool used to interact with the cluster

> **Important:** All steps in this section must be completed on every VM (controlplane-1, 
> controlplane-2, worker-1, worker-2).

---

## Steps

### A) Add the Kubernetes yum Repository: [Kubernetes Package Repo Docs](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/change-package-repository/)

Add the Kubernetes package repository so dnf knows where to find and securely download Kubernetes packages:
```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/repodata/repomd.xml.key
EOF
```

---

### B) Install kubeadm, kubelet, and kubectl
```bash
sudo dnf install -y kubeadm kubelet kubectl
```

---

### C) Enable and Start kubelet
```bash
sudo systemctl enable --now kubelet
```

---

### D) Version Lock kubeadm, kubelet, and kubectl

Lock the versions of all three components to prevent them from being automatically updated during routine system updates like the one done earlier. Unintended version upgrades can cause compatibility issues for the cluster.
```bash
sudo dnf install -y python3-dnf-plugin-versionlock
sudo dnf versionlock kubeadm kubelet kubectl
sudo dnf versionlock list
```

---

> **Important:** Confirm that steps A through D have been completed on all four VMs before 
> proceeding to Part 6.

---

> **Next:** [Part 6: HaProxy Setup](../Part%206:%20HaProxy%20Setup/)
