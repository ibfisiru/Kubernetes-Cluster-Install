# Firewall Rules

A consolidated reference of all firewall rules required for the cluster, organized by host type.

---

## Control Plane Nodes (controlplane-1, controlplane-2)

| Port | Protocol | Purpose |
|------|----------|---------|
| 6443 | TCP | Kubernetes API server |
| 2379-2380 | TCP | etcd |
| 10250 | TCP | Kubelet API |
| 10251 | TCP | kube-scheduler |
| 10252 | TCP | kube-controller-manager |
```bash
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --permanent --add-port=2379-2380/tcp
sudo firewall-cmd --permanent --add-port=10250/tcp
sudo firewall-cmd --permanent --add-port=10251/tcp
sudo firewall-cmd --permanent --add-port=10252/tcp
sudo firewall-cmd --permanent --add-masquerade
sudo firewall-cmd --reload
```

---

## Worker Nodes (worker-1, worker-2)

| Port | Protocol | Purpose |
|------|----------|---------|
| 10250 | TCP | Kubelet API |
| 30000-32767 | TCP | NodePort Services |
```bash
sudo firewall-cmd --permanent --add-port=10250/tcp
sudo firewall-cmd --permanent --add-port=30000-32767/tcp
sudo firewall-cmd --permanent --add-masquerade
sudo firewall-cmd --reload
```

---

## Bastion Host (HAProxy)

| Port | Protocol | Purpose |
|------|----------|---------|
| 6443 | TCP | Kubernetes API server (HAProxy frontend) |
```bash
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --reload
```

---

> **Note:** The `--add-masquerade` rule enables pod network traffic and is required on all 
> cluster nodes (control plane and worker). It is not required on the bastion host.
