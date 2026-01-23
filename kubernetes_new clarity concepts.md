### ExternalName is a Kubernetes Service type that maps an internal Kubernetes service name to an external DNS name using DNS (CNAME), without creating pods, IPs, or load balancers.
* Kubernetes stores this as a DNS rule

---
 ### What can you use for batch or one-time tasks?
job: "Do this task now, make sure it finishes"
You define how many pods should run (usually 1, but can be more for parallelism).
Kubernetes ensures that each pod completes its task successfully.
Once all pods finish, the Job is done.
Example use case: migrate a database, run a batch script once.
CronJob: "Do this task every day/hour/minute, make sure it finishes each time"
---
###  What is a ServiceAccount?
* A ServiceAccount is an identity for pods, allowing them to access the Kubernetes API securely, and is used with RBAC for permission control.

---
### 50. What is PodSecurity (PSA)?
PodSecurity Admission (PSA) Simplified:
PSA is like a security gatekeeper for pods.
Every time a pod is created, PSA checks it against security rules.
The rules are defined at the namespace level using labels (enforce, audit, warn).
Based on the rules, PSA can:
Allow the pod if it meets the rules
Deny the pod if it violates rules
Audit/Log violations without blocking (for monitoring)
---
###  How Kubernetes self-heals StatefulSets
Node failure detected
StatefulSet controller notices pod is down
Tries to schedule pod on available node (if storage allows)
Pod starts with same name, same volume
Simple Analogy
Stateless pod: Can start anywhere → no problem
Stateful pod: Needs its own “desk” (volume) → may have to wait if desk isn’t movable

---
### 59. What is admission controller?
An admission controller is a Kubernetes gatekeeper that checks or modifies requests before they are saved in the cluster. Kubernetes uses built-in admission controllers like LimitRanger to enforce CPU/memory limits and ResourceQuota to prevent overuse of cluster resources.

Key clarification (important)
❌ You usually do NOT create LimitRanger or ResourceQuota as controllers
✅ They are built-in admission controllers
You configure them using:
LimitRange objects
ResourceQuota objects
---
### Questions 52 to 58 in kubernetes.

---

### what exactly is the difference between stateless set and stateless set?
* “State is the data an application needs to keep.
* In a stateless application, the pod does not store data locally; any required data is stored outside the pod, like in a database or object storage.
* In a stateful application, the pod needs to keep its data, and that data must persist even if the pod restarts.”

---

### 1️⃣ **Stateless application**

* Runs as a **Deployment**
* Exposed via a **normal Service** (ClusterIP / NodePort / LoadBalancer)

Example:

```
app.default.svc.cluster.local
```

This service is used by:

* users
* other services
* ingress / load balancer

---

### 2️⃣ **Stateful application (Database)**

* Runs as a **StatefulSet**
* Uses a **Headless Service** for stable pod identity

Example DNS:

```
mysql-0.mysql.default.svc.cluster.local
mysql-1.mysql.default.svc.cluster.local
```

Used for:

* replication
* leader election
* internal DB communication

---

### 3️⃣ **How stateless app connects to stateful DB**
* NOTE: StatefulSet can be exposed with both a headless service and a normal ClusterIP service at the same time, and in fact, this is the standard pattern for databases in Kubernetes.
* Stateful applications use headless services for internal replication, clustering, and leader election, while stateless pods communicate with stateful pods via a normal ClusterIP service.

Example:

```
DB_HOST=mysql.default.svc.cluster.local
```

---

| Platform | Command                        | What it shows                  |
| -------- | ------------------------------ | ------------------------------ |
| K8s      | `kubectl get pods`             | Pod status                     |
| K8s      | `kubectl describe pod <name>`  | Events, container status       |
| K8s      | `kubectl logs <pod>`           | Container logs                 |
| K8s      | `kubectl top pod --containers` | CPU & Memory per container     |
| Docker   | `docker ps`                    | Running containers             |
| Docker   | `docker logs <container>`      | Container logs                 |
| Docker   | `docker stats`                 | CPU, Memory, Network, Disk I/O |

---
