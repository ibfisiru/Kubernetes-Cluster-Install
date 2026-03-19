# Part 6: HAProxy Setup

HAProxy is a load balancer that will sit in front of the two control plane nodes. Instead of pointing kubectl and worker nodes directly at a single control plane, they point to HAProxy and  distributes traffic between both control plane nodes. This gives the cluster a highly available control plane. If one control plane node goes down, the other continues to serve traffic.

> **Note:** All steps in this section are performed on the **bastion host** only, not on the VMs.

---

## Steps (On the bastion host (Fedora Workstation))

### A) Install HAProxy
```bash
sudo dnf install -y haproxy
```

---

### B) Configure HAProxy

Edit the HAProxy configuration file and add the following to the bottom of the file:
```bash
sudo vi /etc/haproxy/haproxy.cfg
```

Add the following:
```
frontend kubernetes-frontend
    bind *:6443
    mode tcp
    default_backend kubernetes-backend

backend kubernetes-backend
    mode tcp
    balance roundrobin
    server <controlplane-1-hostname> <controlplane-1-IP>:6443 check
    server <controlplane-2-hostname> <controlplane-2-IP>:6443 check
```

> Replace `<controlplane-1-hostname>`, `<controlplane-2-hostname>`, `<controlplane-1-IP>`, 
> and `<controlplane-2-IP>` with your actual VM hostnames and IP addresses.

---

### C) Open Port 6443 on the Bastion Host to allow Kubernetes API traffic through the firewall on the bastion host:
```bash
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --reload
```

---

### D) Enable and Start HAProxy
```bash
sudo systemctl enable --now haproxy
sudo systemctl status haproxy
```

---

> **Next:** [Part 7: Cluster Initialization](../Part%207:%20Cluster%20Initialization/)

