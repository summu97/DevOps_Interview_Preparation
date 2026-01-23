### ExternalName is a Kubernetes Service type that maps an internal Kubernetes service name to an external DNS name using DNS (CNAME), without creating pods, IPs, or load balancers.

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
