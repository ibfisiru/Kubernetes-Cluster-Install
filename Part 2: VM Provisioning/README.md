# Part 2: VM Provisioning

Create the four virtual machines that will serve as the Kubernetes cluster nodes. All the VMs 
will run Fedora 43 Server. Two will be configured as control plane nodes and two as worker nodes.

> **Note:** All four VMs should be created with the same specs. Resource allocation below reflects a homelab environment and should be adjusted based on your hardware and workload requirements.

---

## VM Specifications

| Node | Role | vCPU | RAM | Storage |
|------|------|------|-----|---------|
| controlplane-1 | Control Plane | 4 | 6GiB | 30GiB |
| controlplane-2 | Control Plane | 4 | 6GiB | 30GiB |
| worker-1 | Worker | 4 | 6GiB | 30GiB |
| worker-2 | Worker | 4 | 6GiB | 30GiB |

---

## Steps

### A) Download Fedora 43 Server ISO

Download the Fedora 43 Server ISO to use as the OS for each VM:

[Download Fedora 43 Server ISO Here](https://fedoraproject.org/server/download/)

---

### B) Create each VM in Virtual Machine Manager

Using Virtual Machine Manager, create all four VMs using the Fedora 43 Server ISO with 
the specifications from the table above. Repeat this process for each VM.

---

> **Next:** [Part 3: Node Configuration](../Part%203:%20Node%20Configuration/)

