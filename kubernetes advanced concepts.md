## What are Taints and Tolerations?
* **Taints** is Applied to nodes: They tell Kubernetes not to schedule pods on tainted nodes.
```bash
kubectl taint nodes <node_name> <key>=<value>:<taint_effect>

# Taint effects
# > NoSchedule
# > PreferNoSchedule
# > NoExecute
```

* **Tolerations** is Applied to pods They tell Kubernetes: This pod is allowed to run on nodes with this taint.


---

### Taint Effects (VERY IMPORTANT)

### 1️⃣ `NoSchedule`

* New pods **will NOT be scheduled** on the node
* Existing pods are **not affected**

📌 Example:

```bash
kubectl taint nodes node-1 dedicated=prod:NoSchedule
```

---

### 2️⃣ `PreferNoSchedule`

* Kubernetes **tries to avoid** scheduling pods on the node
* Not a hard rule

📌 Example:

```bash
kubectl taint nodes node-1 dedicated=batch:PreferNoSchedule
```

---

### 3️⃣ `NoExecute`

* New pods **will not be scheduled**
* Existing pods **will be evicted** unless they tolerate the taint

📌 Example:

```bash
kubectl taint nodes node-1 maintenance=true:NoExecute
```

---

### How to add a **toleration** to a Pod

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "prod"
  effect: "NoSchedule"
```

This pod can now run on nodes tainted with:

```
dedicated=prod:NoSchedule
```

---

### Example: Dedicated production nodes

### Step 1: Taint node

```bash
kubectl taint nodes prod-node dedicated=prod:NoSchedule
```

### Step 2: Add toleration to pod/deployment

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "prod"
  effect: "NoSchedule"
```

---

### Remove a taint

```bash
kubectl taint nodes node-1 dedicated=prod:NoSchedule-
```

---

### Check taints on nodes

```bash
kubectl describe node node-1 | grep -i taint
```

---
