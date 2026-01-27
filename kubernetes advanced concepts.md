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

## What is a **Node Selector**?

> A **nodeSelector** is a way to tell Kubernetes to schedule a pod **only on nodes that have specific labels**.

In short:

* **Labels → applied on nodes**
* **nodeSelector → applied on pods**
* Pods run **only on matching nodes**

---

### Example: Add a label to a node

```bash
kubectl label nodes node_name env=prod
```

---

### Use nodeSelector in a Pod / Deployment

```yaml
nodeSelector:
  env: prod
```

This pod will only run on nodes labeled:

```
env=prod
```

---

### If no node matches?

* Pod stays in **Pending** state
* Scheduler logs will show:

  > 0/5 nodes are available: node(s) didn't match node selector

---

### Check node labels

```bash
kubectl get nodes --show-labels
```

---

### Remove a node label

```bash
kubectl label nodes node-1 env-
```

---
## What is Node Affinity in Kubernetes?

**Node affinity** controls **which nodes a Pod is allowed (or prefers) to run on**, based on **node labels**.

Think of it as:

> “Schedule this Pod only / preferably on nodes that match these conditions.”

It’s part of Kubernetes **advanced scheduling**.

---

### Why do we need Node Affinity?

`nodeSelector` is **simple but limited**:

* Only exact match
* No OR / NOT conditions
* No preference-based scheduling

**Node affinity fixes this** by allowing:

* Required vs Preferred rules
* Multiple conditions
* Operators like `In`, `NotIn`, `Exists`

---

### Types of Node Affinity

### 1️⃣ RequiredDuringSchedulingIgnoredDuringExecution

**Hard rule** ❌
Pod **will not schedule** if no node matches.

> Similar to nodeSelector, but more expressive.

### 2️⃣ PreferredDuringSchedulingIgnoredDuringExecution

**Soft rule** ✅
Kubernetes **tries** to place the pod on matching nodes,
but **will still schedule elsewhere** if needed.

---

### Common Operators

| Operator       | Meaning                         |
| -------------- | ------------------------------- |
| `In`           | Label value must be in list     |
| `NotIn`        | Label value must NOT be in list |
| `Exists`       | Label key must exist            |
| `DoesNotExist` | Label key must NOT exist        |
| `Gt`           | Greater than (numbers)          |
| `Lt`           | Less than (numbers)             |

---

### Example 1: Required Node Affinity

👉 Run pod **only on nodes with `env=prod`**

### Label the node

```bash
kubectl label node node-1 env=prod
```

### Pod spec

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: env
          operator: In
          values:
          - prod
```

🧠 If no node has `env=prod` → **Pod stays Pending**

---

### Example 2: Preferred Node Affinity

👉 Prefer nodes with `disk=ssd`, but not mandatory

```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 1
      preference:
        matchExpressions:
        - key: disk
          operator: In
          values:
          - ssd
```

🧠 Kubernetes tries SSD nodes first, but will fall back if needed.

---

### Node Selector vs Node Affinity

| Feature             | nodeSelector | nodeAffinity |
| ------------------- | ------------ | ------------ |
| Exact match only    | ✅            | ❌            |
| OR / NOT conditions | ❌            | ✅            |
| Preferred rules     | ❌            | ✅            |
| Complex logic       | ❌            | ✅            |

---

### Real-world DevOps Use Cases

✔️ Run DB pods only on high-memory nodes
✔️ Separate prod & non-prod workloads
✔️ Place latency-sensitive apps on SSD nodes
✔️ Control cost by steering workloads to cheaper nodes

---

If you want, next we can cover:

* **Pod affinity vs anti-affinity**


