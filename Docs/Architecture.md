# Architecture

This document covers the design decisions behind the cluster and the role each component 
plays in the overall setup.

---

## Design Decisions

### Why Fedora Linux?
Fedora was chosen as the operating system for both the bastion host and the cluster nodes because it is community-driven distribution that ships with recent kernel versions and up to date packages.  It is also the upstream source for Red Hat Enterprise Linux (RHEL), making it a good choice for those who work in or are preparing for enterprise and federal environments where RHEL is common.

### Why KVM/QEMU + libvirt?
KVM/QEMU is the native hypervisor stack for Linux. It is built into the kernel and performs well without the overhead of a third party hypervisor. libvirt provides the management layer on top of KVM/QEMU, and Virtual Machine Manager provides a GUI for creating and managing VMs. This stack is widely used in enterprise environments and is a natural fit for a Fedora host.

### HAProxy
A highly available Kubernetes cluster requires a load balancer in front of the control plane nodes so that the API server has a single stable endpoint. HAProxy was chosen because it is lightweight, straightforward to configure for TCP pass-through, and well suited for a homelab environment. In a cloud environment this role would typically be filled by a managed load balancer such as an AWS ALB (Application load balancer) or NLB (Network load balancer).

### Calico
Calico provides robust network policy support and performs well at scale. For this homelab setup it handles pod-to-pod communication across nodes using BGP-based routing.

### containerd
Containerd is the industry standard container runtime for Kubernetes. It was originally part of Docker but was extracted into a standalone project and is now the default runtime used by most Kubernetes distributions. It handles the full container lifecycle including image pulls, storage, and network integration.

---

## Component Roles

### Bastion Host (Fedora Workstation)
The bastion host is the main Fedora Workstation machine that runs the hypervisor and hosts all four cluster VMs. It also runs HAProxy, serving as the load balancer for the Kubernetes API server. It is the single entry point for cluster administration.

### Control Plane Nodes (controlplane-1, controlplane-2)
The control plane nodes run the core Kubernetes components that manage the state of the cluster:

- **kube-apiserver**: This is the front end for the Kubernetes control plane. All communication with the cluster goes through the API server
- **etcd**: Is the distributed key-value store that holds all cluster state and configuration
- **kube-scheduler**: Watches for newly created pods and assigns them to nodes based on available resources
- **kube-controller-manager**: Runs the core control loops that regulate the state of the cluster

Having two control plane nodes provides high availability. If one control plane node goes down, HAProxy routes traffic to the other and the cluster continues to operate.

### Worker Nodes (worker-1, worker-2)
Worker nodes are where the actual application workloads run. Each worker node runs:
- **kubelet**: The agent that communicates with the control plane and manages containers 
on the node
- **kube-proxy**: Manages network rules on the node to allow communication to pods
- **containerd**: The container runtime that manages the lifecycle of containers on the node

### Calico
Calico runs on all nodes and provides pod networking. It assigns each pod an IP address and handles routing so pods can communicate with each other across nodes regardless of which node they are scheduled on.

### HAProxy
HAProxy runs on the bastion host and listens on port 6443. It distributes incoming Kubernetes API traffic between the two control plane nodes. All kubectl commands and node join requests are routed through HAProxy.
