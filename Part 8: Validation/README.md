# Part 8: Validation

With all nodes joined, its time to verify that the cluster is fully operational and that all nodes are in a healthy state.

> **Note:** Steps are performed on the **controlplane-1** VM.

---

## Steps

### A) Verify All Nodes are Ready

Run the following command to confirm all four nodes are joined and in a `Ready` state:
```bash
kubectl get nodes
```

Expected output should show all four nodes with a status of `Ready`:
```
NAME              STATUS   ROLES           AGE   VERSION
controlplane-1    Ready    control-plane   Xm    v1.x.x
controlplane-2    Ready    control-plane   Xm    v1.x.x
worker-1          Ready    <none>          Xm    v1.x.x
worker-2          Ready    <none>          Xm    v1.x.x
```

---

