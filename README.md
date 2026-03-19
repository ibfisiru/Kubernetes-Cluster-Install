# Technical write of a Kubernetes cluster installation
A practical homelab guide for deploying a highly available, multi-node Kubernetes cluster 
on a local machine using KVM/QEMU virtual machines. This project documents the full 
installation process from hypervisor setup through cluster validation.


> **Note:** This guide is intended for those who already have an understanding of Kubernetes and its core components (control plane, kubelet, etcd, CNI, etc.). It does not cover Kubernetes concepts rather the installation process.

> **Distribution:** All steps written in this project are for the **Fedora Linux** distribution. The workflow is also compatible with **Red Hat Enterprise Linux (RHEL)**. Other distributions (Ubuntu, Debian, Arch, etc.) can follow the same general workflow but package managers and some commands will differ. Please see documentation for distribution of Linux

> **Automation:** Many of the commands in this guide can be batched into scripts. However, all steps are intentionally manual to provide insight into how each step contributes to the overall process of standing up a Kubernetes cluster.

> **Bastion Host:** Throughout this guide, the term *bastion host* refers to the main Fedora Workstation machine that the VMs are running on.


## How To Use This Guide
Each directory in this repo corresponds to a phase of the installation process. Work through them in order because each phase builds on the previous one.



## Prerequisites
- Fedora Workstation 43 is needed to setup the hypervisor and VMs: [Download Fedora Workstation ISO Here](https://fedoraproject.org/workstation/download/)
- Sufficient hardware to run 4 VMs simultaneously (see cluster architecture table below)
- Basic familiarity with the Linux command line
- Basic familiarity with virtualization concepts

## Cluster Architecture
>These figures reflect that of a homelab and will need to be adjusted based on your use case and projected workload(s) on the cluster

| Node | Role | vCPU | RAM | Storage |
|------|------|------|-----|---------|
| controlplane-1 | Control Plane | 4 | 6GiB | 30GiB |
| controlplane-2 | Control Plane | 4 | 6GiB | 30GiB |
| worker-1 | Worker | 4 | 6GiB | 30GiB |
| worker-2 | Worker | 4 | 6GiB | 30GiB |
| Bastion Host | Hypervisor / HAProxy LB | - | - | - |


**Stack:**
- Hypervisor: KVM/QEMU + libvirt
- OS (nodes): Fedora 43 Server
- Container Runtime: containerd
- Kubernetes Version: v1.31
- CNI Plugin: Calico v3.29.3
- Load Balancer: HAProxy



### Additional Reference
- [docs/architecture.md](./docs/architecture.md): Design decisions and rationale
- [docs/firewall-rules.md](./docs/firewall-rules.md): All firewall rules by host type in one place
- [docs/troubleshooting.md](./docs/troubleshooting.md): Common issues encountered during the build
- [Kubernetes Source Material](https://kubernetes.io/docs/home/): Kubernetes Open Source Documentation






