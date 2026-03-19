# Part 7: Cluster Initialization

With all nodes configured and HAProxy running, the cluster can now be initialized. This section covers initializing the control plane, installing the Calico CNI plugin, and joining all remaining nodes to the cluster.

> **Note:** Steps are performed on the **controlplane-1** VM.

---

## Steps

### A) Initialize the Cluster

On controlplane-1, run kubeadm to initialize the cluster. This command points the cluster 
at the HAProxy load balancer as the control plane endpoint:
```bash
sudo kubeadm init --control-plane-endpoint "<HAProxy-Bastion-IP>:6443" --upload-certs --pod-network-cidr=<PodCIDR/16>
```

- `--control-plane-endpoint`: Points to the bastion host's HAProxy IP and port
- `--upload-certs`: Automatically shares certificates between control plane nodes so other control plane nodes can join
- `--pod-network-cidr`: The CIDR range that pods will use for networking

> **Important:** The output of this command will produce two join commands, one for control plane nodes and one for worker nodes. **Save both of these commands.** They will be needed in steps E and F.

---

### B) Set Up kubectl Access on controlplane-1

Configure kubectl so you can interact with the cluster from controlplane-1:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

### C) Verify the Cluster is Running

Confirm the cluster initialized correctly. The node status will show `NotReady` at this point because the CNI plugin has not been installed yet:
```bash
kubectl get nodes
# Status will show NotReady. This is expected until Calico is installed
```

---

### D) Install CNI Network Plugin (Calico)

Calico is the CNI network plugin that allows pods to communicate across nodes.

Download the Calico manifest on controlplane-1:
```bash
curl -L https://raw.githubusercontent.com/projectcalico/calico/v3.29.3/manifests/calico.yaml -o calico.yaml
```

Edit the manifest to set the pod CIDR to match what you passed to kubeadm init:
```bash
vi calico.yaml
```

Find the `CALICO_IPV4POOL_CIDR` section, uncomment it, and set the value (for example):
```yaml
# Uncomment and set:
- name: CALICO_IPV4POOL_CIDR
  value: "10.244.0.0/16"   # Replace with your --pod-network-cidr
```

Apply the manifest:
```bash
kubectl apply -f calico.yaml
```

Wait a moment for the Calico pods to start then verify all nodes are in a `Ready` state:
```bash
kubectl get nodes
```

---

### E) Join the Second Control Plane Node

Logon to **controlplane-2** and run the control plane join command that was produced in step A:
```bash
# Run the control plane join command from the kubeadm init output
```

---

### F) Join the Worker Nodes

Logon to **each worker node** and run the worker join command that was produced in step A:
```bash
# Run the worker join command from the kubeadm init output
```

---

> **Next:** [Part 8: Validation](../Part%208:%20Validation/)
