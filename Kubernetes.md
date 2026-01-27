### How do you give access to aks cluster at infra level(through azure portal UI)?
* Create a user or group in Azure Active Directory (Entra ID) and assign an Azure role (Reader / Contributor) on the AKS resource, resource group, or subscription to grant infra-level (portal) access.

> ✔ They can view / manage AKS in the Azure Portal
>
> ❌ They cannot use kubectl unless Kubernetes RBAC is configured

---

### How do you restrict users in aks cluster at cluster level or i have new users, how do i give them cluster access with limited priviledges?
* Create an Azure AD group, add users to it, then create a Kubernetes Role/ClusterRole and bind it to that Azure AD group using RoleBinding/ClusterRoleBinding.

> “Azure RBAC controls infra-level access to AKS through the portal, while Kubernetes RBAC—integrated with Azure AD groups—controls in-cluster access via kubectl.”

---

### Scan a Docker image for vulnerabilities?
```bash
trivy image nginx:latest
trivy image --severity HIGH,CRITICAL nginx:latest
```

---

### What Canary Deployment Means in Kubernetes an how i implemented it?
* Canary deployment means releasing a new version to a small portion of users first, while the old version is still running, and then gradually increasing traffic if everything looks good.
```bash
I already have v1 running with its Deployment, Service, and Ingress.
Then I deploy v2 as a separate Deployment with its own Service, and create a new Ingress resource with canary rules to gradually route traffic to v2.
```

### What Blue Green Deployment Means in Kubernetes an how i implemented it?

> I already have **Blue (v1)** running with its Deployment and Service.
> I deploy **Green (v2)** as a separate Deployment with its own Service.
> Once v2 is verified, I **switch all traffic at once** (via Ingress/Service) from Blue to Green.

### Key difference vs Canary (one-liner 🧠)

* **Canary** → gradual traffic (5%, 10%, 50%…)
* **Blue-Green** → instant switch (0% → 100%)

---

### What are StatefulSets?
* StatefulSets are Kubernetes objects used for applications that need a stable identity, such as fixed pod names, stable network identities, and dedicated persistent storage (one PV/PVC per pod).
They are commonly used for databases, Kafka, and other stateful applications.
* StatefulSets are used for stateful applications that need stable pod identity and persistent storage, like databases and Kafka.

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



### Advantages of Helm over deployment files.
| #     | Advantage                              | How to say it in interview                                                                                      |
| ----- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **1** | **Templating & Reusability**           | *Helm allows reusable templates, so the same chart can be used across dev, QA, and prod with different values.* |
| **2** | **Environment-specific configuration** | *Helm uses `values.yaml` to manage environment-specific configs without duplicating YAML files.*                |
| **3** | **Versioning & Rollbacks**             | *Helm keeps release history and allows easy rollback using `helm rollback`.*                                    |
| **4** | **Easy Upgrades**                      | *Helm provides controlled and atomic upgrades using `helm upgrade`.*                                            |
| **5** | **Dependency Management**              | *Helm manages application dependencies like databases via `Chart.yaml`.*                                        |

---

### What are the recent changes in NGINX with Kubernetes?
* Kubernetes has officially announced that the community-maintained Ingress NGINX project will be retired in March 2026. 
* After that:
> There will be no further releases, bug fixes, or security patches from the SIG Network or Kubernetes Security Response Committee.
> The repositories will be maintained read-only for reference.
> Existing deployments will continue to work, but running them without updates poses a security and compatibility risk.
---
### What do we need for **Gateway API**?

### **Answer:**

For **Gateway API**, traffic management is split into **infrastructure objects** and **application routing objects**.
* Infra team → GatewayClass & Gateway
* App team → Routes (HTTPRoute)

✔ Gateway Controller
✔ GatewayClass
✔ Gateway
✔ Route (`HTTPRoute`, `TCPRoute`, etc.)


## 🧩 **Simple Example Flow (Interview-friendly)**

> 1. Install a **Gateway Controller**
> 2. Create a **GatewayClass** to bind the controller
> 3. Create a **Gateway** to expose traffic
> 4. Create **HTTPRoute** to route traffic to Services
---

## 🔁 **Ingress vs Gateway API (Mapping)**

| Ingress            | Gateway API                         |
| ------------------ | ----------------------------------- |
| Ingress Controller | **Gateway Controller**              |
| IngressClass       | **GatewayClass**                    |
| Ingress resource   | **HTTPRoute / TCPRoute / TLSRoute** |

---

## 🧱 **What components are required in Gateway API?**

### 1️⃣ **Gateway Controller**

* A controller that **implements Gateway API**
* Examples:
  * NGINX Gateway Controller
  * Istio
  * Envoy Gateway
  * Traefik

👉 This is equivalent to the **Ingress Controller**

---

### 2️⃣ **GatewayClass**

* Defines **which controller** will manage Gateways
* Created **once per controller**
* Example:
```yaml
kind: GatewayClass
controllerName: gateway.nginx.org/nginx-gateway-controller
```

👉 Equivalent to **IngressClass**

---

### 3️⃣ **Gateway**

* Represents the **actual load balancer / entry point**
* Defines:
  * Listeners (HTTP, HTTPS, TCP)
  * Ports
  * TLS config
* Usually managed by **platform / infra team**

👉 This replaces the **Ingress Controller Service + config**

---

### 4️⃣ **Routes (Application-level objects)**

Depending on traffic type:
* `HTTPRoute` → HTTP/HTTPS
* `TCPRoute` → TCP traffic
* `TLSRoute` → TLS passthrough
* `UDPRoute` → UDP traffic

👉 These replace the **Ingress resource**


---

## ❓ **In what ways is Gateway API better than Ingress?**

### **Answer:**

1. **Better separation of responsibilities**

   * Ingress mixes **infrastructure and application routing** in one object.
   * Gateway API separates them clearly:

     * Infra team → `GatewayClass`, `Gateway`
     * App team → `HTTPRoute`, `TCPRoute`

2. **More expressive and flexible routing**

   * Ingress is limited to **host + path rules**.
   * Gateway API supports:

     * Header-based routing
     * Method-based routing
     * Query param routing
     * Weighted traffic splitting (canary, blue-green)

3. **Native support for multiple protocols**

   * Ingress is mostly **HTTP/HTTPS-focused**.
   * Gateway API natively supports:

     * HTTP, HTTPS
     * TCP
     * UDP
     * TLS passthrough

4. **Role-based access control (RBAC friendly)**

   * Gateway API allows fine-grained permissions:

     * App teams can manage Routes
     * Platform teams control Gateways
   * Prevents app teams from accidentally breaking infra.

5. **Standardized, portable API**

   * Ingress behavior depends heavily on **controller-specific annotations**.
   * Gateway API uses **standard fields**, reducing vendor lock-in.

6. **Explicit attachment model**

   * Routes explicitly reference which Gateway they attach to.
   * Makes traffic flow **clear and predictable**.
   * Ingress implicitly binds to controllers.

7. **Better support for multi-tenancy**

   * Multiple teams can safely share the same Gateway.
   * Namespace and policy isolation is built-in.

8. **Designed for extensibility**

   * Gateway API supports **policies** (timeouts, retries, rate limits).
   * Easier to extend without breaking compatibility.

9. **Future-proof and actively evolving**

   * Ingress is considered **feature-complete**.
   * Gateway API is the **future standard** backed by Kubernetes SIG Network.
---
## 1️⃣ Pod (single container)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

---

## 2️⃣ ReplicaSet (ensures fixed number of pods)

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

📌 **Use case**:
Maintains pod count but **no rolling updates** → usually managed by Deployments.

---

## 3️⃣ Deployment (most commonly used)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

📌 **Use case**:
✔ Rolling updates
✔ Rollbacks
✔ Auto-creates ReplicaSet
✔ Production standard

---

## 4️⃣ Service (ClusterIP example)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

📌 **Use case**:
Provides **stable networking** and load balancing for pods.

🔁 Other types:

* `NodePort`
* `LoadBalancer`
* `ExternalName`

---

## 5️⃣ DaemonSet (one pod per node)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
spec:
  selector:
    matchLabels:
      app: nginx-daemon
  template:
    metadata:
      labels:
        app: nginx-daemon
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

📌 **Use case**:
Runs **one pod on every node**
Examples:

* Log agents (Fluentd)
* Monitoring (Node Exporter)
* Security agents

---
### What is **ExternalName Service — Definition + Complete Flow (Simple & Clear)**

---

## ✅ Definition

> **ExternalName is a Kubernetes Service type that maps an internal Kubernetes service name to an external DNS name using DNS (CNAME), without creating pods, IPs, or load balancers.**

---

## 🔁 Complete Flow (Step by Step)

### 1️⃣ You create an ExternalName service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-api
spec:
  type: ExternalName
  externalName: api.company.com
```

---

### 2️⃣ Kubernetes stores this as a DNS rule

* No pod
* No ClusterIP
* No Endpoints
* Only **DNS entry**

---

### 3️⃣ A pod makes a request

Inside the cluster, an app calls:

```
external-api.default.svc.cluster.local
```

---

### 4️⃣ CoreDNS resolves the name

CoreDNS replies:

```
external-api.default.svc.cluster.local → CNAME → api.company.com
```

---

### 5️⃣ Pod connects directly to external service

* Kubernetes is **out of the data path**
* Traffic goes **straight to api.company.com**
* TLS, retries, auth → handled by the app or external service

---

## 🧠 Visual Flow

```
Pod
 ↓
external-api.default.svc.cluster.local
 ↓ (DNS CNAME)
api.company.com
 ↓
External Service (DB / API / Legacy App)
```

---

## ✅ When to use ExternalName

* Calling **external DB**
* Accessing **third-party APIs**
* Same service name across **dev / prod**
* Avoid hardcoding external URLs in code

---

# 🔹 Kubernetes – Theoretical Interview Questions

### 0. What is Kubernetes DNS?
- “Kubernetes DNS is provided by CoreDNS, which allows pods to discover services using DNS names. CoreDNS watches the Kubernetes API and dynamically creates DNS records for services and pods. When a pod queries a service name, CoreDNS returns the service IP or pod IPs, enabling reliable service discovery despite dynamic pod lifecycles.”
- 🔹 Kubernetes DNS Naming Format
     * Full DNS Name: <service>.<namespace>.svc.cluster.local
- 🔹 StatefulSet DNS
     * Each pod gets its own DNS entry: podname.service.namespace.svc.cluster.local
- 🔹 Pod DNS Configuration
     * Each pod has: cat /etc/resolv.conf
```bash
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
```

### What is Headless Service?
- “A headless service is a Kubernetes service with clusterIP: None. It doesn’t provide load balancing; instead, it exposes the individual pod IPs through DNS, allowing clients to directly communicate with specific pods. It’s commonly used with StatefulSets for stateful applications.”
```bash
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
  - port: 3306

# DNS Query: mysql.default.svc.cluster.local
# DNS Response: 10.244.1.5
```

### What are different DNS records?
- A Record: Maps a domain name → IPv4 address
```bash
example.com → 54.23.10.5
```
- AAAA Record: Maps a domain name → IPv6 address
```bash
example.com → 2001:db8::1
```
- CNAME (Canonical Name): Maps one domain name → another domain name
```bash
www.example.com → example.com
```

## 1️⃣ Kubernetes Fundamentals

### 1. What is Kubernetes and why is it used?

---

### 2. What problems does Kubernetes solve that Docker alone cannot?

---

### 3. Explain Kubernetes architecture at a high level.

---

### 4. What is a **cluster** in Kubernetes?

---

### 5. Difference between:
  * Containerization vs Orchestration

---

## 2️⃣ Kubernetes Architecture (Control Plane & Nodes)

###  6. Explain the role of:
   * API Server
   * etcd
   * Scheduler
   * Controller Manager

---

### 7. What happens if **etcd goes down**?

---

### 8. What is a **node**?

---

### 9. What components run on a worker node?

---

### 10. What is **kubelet**?

---

## 3️⃣ Pods & Containers

### 11. What is a Pod?

---

### 12. Why is Pod the smallest deployable unit?

---

### 13. Can a Pod have multiple containers? When would you do that?

---

### 14. What happens when a Pod dies?
- When a pod dies, Kubernetes controllers detect the failure and create a new pod to maintain the desired state, but standalone pods without a controller are gone permanently.

---

### 15. Difference between:
* Pod vs Deployment
* Pod vs ReplicaSet

---

## 4️⃣ Workloads

### 16. What is a Deployment?
- A Deployment is a Kubernetes resource that manages a set of identical pods, ensuring the desired number of replicas are running and providing updates/rollbacks automatically.

---

### 17. Difference between:
* Deployment
* StatefulSet
- A StatefulSet is a Kubernetes resource for managing stateful applications, providing stable pod identities, persistent storage, and ordered deployment.
- When to use: Databases (MySQL, PostgreSQL, MongoDB), Kafka, Zookeeper

* DaemonSet
* Job
- "Do this task now, make sure it finishes"

### What can you use for batch or one-time tasks?
- job: "Do this task now, make sure it finishes"
  * You define how many pods should run (usually 1, but can be more for parallelism).
  * Kubernetes ensures that each pod completes its task successfully.
  * Once all pods finish, the Job is done.
  * Example use case: migrate a database, run a batch script once.

* CronJob: "Do this task every day/hour/minute, make sure it finishes each time"
- It is just like cronjob:
  * You define a schedule (like Linux cron: */5 * * * *).
  * Kubernetes automatically creates a Job at each scheduled time.
  * Each Job behaves like a normal Job — it ensures pods complete successfully.
  * Example use case: backup database every night, clean temp files every hour.

---

### 18. What is a ReplicaSet?

---

### 19. How does Kubernetes maintain desired state?
User YAML → etcd stores desired state
      ↓
Controller monitors actual state
      ↓
If mismatch → Controller fixes it → Cluster matches desired state

- Kubernetes maintains desired state by continuously comparing the current state of the cluster with the desired state defined in manifests and taking corrective actions to reconcile differences.
---

## 5️⃣ Services & Networking

### 20. What is a Service?

---

### 21. Service types:
* ClusterIP
* NodePort
* LoadBalancer
* ExternalName

---

### 22. Difference between:
 * ClusterIP vs NodePort

---

### 23. How does Pod-to-Pod communication work?
- Pod-to-Pod communication in Kubernetes works via pod IPs and Services, allowing direct or load-balanced communication across nodes, optionally controlled by NetworkPolicies.
Pod A ───> Pod B  (direct IP)
Pod A ───> Service ──> Pod B (DNS + load balancing)

---

### 24. What is **kube-proxy**?

---

### 25. How does Kubernetes DNS work?

---

## 6️⃣ Ingress & Traffic Management

### 26. What is Ingress?
- Ingress is a Kubernetes resource that manages external HTTP/S access to services inside a cluster, typically providing routing, SSL termination, and load balancing.

---

### 27. Difference between:
* Ingress
* Service

* **Service:** Like a **virtual IP address** that points to pods
* **Ingress:** Like a **traffic manager / router** that directs requests based on URL or hostname

---

### 🔹 Example

* **Service:** `my-service:80 → pods` (all traffic goes to pods)
* **Ingress:**
  * `example.com/api → api-service`
  * `example.com/web → web-service`

---

### 🎯 One-line Interview Answer
> **Service exposes pods at a stable IP/port (TCP/UDP), while Ingress exposes multiple services to external HTTP/S traffic with routing and optional SSL termination.**


---

### 28. What is an Ingress Controller?
- An Ingress Controller is a component that implements the rules defined in Ingress resources and actually manages the routing of external HTTP/S traffic to services inside a Kubernetes cluster.

---

### 29. Name popular Ingress controllers.
| Ingress Controller | Key Feature                               |
| ------------------ | ----------------------------------------- |
| NGINX              | Widely used, stable                       |
| Traefik            | Lightweight, dynamic routing              |
| HAProxy            | High performance, advanced load balancing |
| Istio Gateway      | Service mesh integration, observability   |
| Contour            | Envoy-based, high performance             |
| AWS ALB            | Cloud-managed, integrates with AWS EKS    |

---

### 30. How do you expose multiple services with one load balancer?

---

## 7️⃣ Config & Secrets

### 31. What is a ConfigMap?

---

### 32. What is a Secret?
- Kubernetes Secrets are base64-encoded

---

### 33. Difference between:
* ConfigMap vs Secret

## Note: Both ConfigMaps and Secrets in Kubernetes are namespace-specific.
---

### 34. How do you mount secrets into pods?
- Secrets can be mounted into pods either as environment variables or as files in a volume, allowing containers to securely access sensitive data.

---

### 35. Why should secrets not be stored in Git?
- Secrets should not be stored in Git because they are sensitive, base64-encoded only, and committing them risks exposure; proper secret management tools should be used instead.

---

## 8️⃣ Storage

### 36. What is a Volume?
- A Kubernetes Volume is a directory accessible to containers in a pod that provides persistent or shared storage beyond the container’s lifecycle.

---

### 37. Difference between:
* emptyDir
* hostPath
- emptyDir is a temporary pod-specific volume deleted when the pod dies, while hostPath mounts a directory from the host node and persists beyond the pod lifecycle.
- emptyDir → Pod’s private notebook → shredded when pod leaves
- hostPath → Node’s shared notebook → stays on the desk even after pod leaves

---

### 38. What is PersistentVolume (PV)?
- A PersistentVolume (PV) is a piece of storage in the cluster provisioned by an admin or dynamically, which pods can use to store data beyond the pod’s lifecycle.

---

### 39. What is PersistentVolumeClaim (PVC)?
- PVC is how pods “ask” for persistent storage. PV is the actual storage, PVC is the request.

---

### 40. Difference between:
* PV vs PVC

---

### 41. What is StorageClass?
- StorageClass is the blueprint for PV
- you Create StorageClass and Create PersistentVolumeClaim
- Flow: Pod asks PVC → PVC asks StorageClass → StorageClass creates PV → Pod uses PV

---

## 9️⃣ Scheduling & Scaling

### 42. What is the Kubernetes scheduler?

### 43. What is HPA?

### 44. What metrics does HPA use?
- HPA uses CPU, memory, custom, or external metrics to automatically scale the number of pods up or down based on load.

### 45. Difference between:
* HPA
* VPA

### 46. What is Cluster Autoscaler?
- Cluster Autoscaler automatically adjusts the number of nodes in a Kubernetes cluster to ensure pods have enough resources and to optimize cluster cost.
Pod requests → Not enough resources on nodes
          ↓
Cluster Autoscaler adds nodes
          ↓
Pods get scheduled successfully

Unused node → Pods can move → Node terminated

---

## 🔟 Security & Access Control

### 47. What is RBAC?
- RBAC (Role-Based Access Control) is a Kubernetes mechanism to control who can do what in the cluster by defining roles and binding them to users, groups, or service accounts.

---

### 48. Difference between:
* Role vs ClusterRole

## Note:
| Component              | Purpose                                                                          |
| ---------------------- | -------------------------------------------------------------------------------- |
| **Role**               | Defines permissions **within a namespace** (e.g., read pods, create deployments) |
| **ClusterRole**        | Defines permissions **cluster-wide**                                             |
| **RoleBinding**        | Assigns a Role to a **user, group, or service account** in a namespace           |
| **ClusterRoleBinding** | Assigns a ClusterRole to a **user, group, or service account** cluster-wide      |

---

### 49. What is a ServiceAccount?
- A ServiceAccount is an identity for pods, allowing them to access the Kubernetes API securely, and is used with RBAC for permission control.

---

### 50. What is PodSecurity (PSA)?
- PodSecurity Admission (PSA) Simplified: 
* PSA is like a security gatekeeper for pods.
* Every time a pod is created, PSA checks it against security rules.
* The rules are defined at the namespace level using labels (enforce, audit, warn).
* Based on the rules, PSA can:
  * Allow the pod if it meets the rules
  * Deny the pod if it violates rules
  * Audit/Log violations without blocking (for monitoring)


### 51. How do you secure a Kubernetes cluster?
- Secure a Kubernetes cluster by controlling access, enforcing pod security rules, managing secrets safely, hardening nodes, securing networking, and monitoring activity.

---

## 🔹 Scenario-Based Kubernetes Interview Questions (VERY IMPORTANT)

---

## 🔥 Scenario 1: Pod in CrashLoopBackOff

**Q:**
Your pod is in `CrashLoopBackOff`. How do you debug it?

**Expected areas:**

* `kubectl logs`
* `kubectl describe pod`
* Exit codes
* Liveness/readiness probes
* Application startup issues

---

## 🔥 Scenario 2: Application Not Accessible

**Q:**
Pod is running but application is not accessible externally.

**Expected checks:**

* Service type
* Port mapping
* Ingress configuration
* Network policies
* Security groups / firewalls

---

## 🔥 Scenario 3: Zero-Downtime Deployment

**Q:**
How do you deploy a new version without downtime?

**Expected discussion:**

* Rolling updates
* Readiness probes
* `maxSurge`, `maxUnavailable`
* Blue-Green / Canary deployments

---

## 🔥 Scenario 4: Pod Scheduling Failure

**Q:**
Pods are stuck in `Pending` state. Why?

**Expected reasons:**

* Insufficient resources
* Node selectors / taints
* PVC not bound
* Image pull issues

---

## 🔥 Scenario 5: Configuration Change Without Restart

**Q:**
You updated a ConfigMap but pods didn’t pick up changes. Why?

**Expected understanding:**

* Mounted vs env-based config
* Rolling restart
* `kubectl rollout restart`

---

## 🔥 Scenario 6: Secrets Management

**Q:**
How do you manage secrets securely in production?

**Expected answers:**

* External secret managers
* Sealed Secrets
* CSI Secret Store
* RBAC

---

## 🔥 Scenario 7: High Resource Usage

**Q:**
A namespace is consuming too many resources. How do you control it?

**Expected tools:**

* Resource requests & limits
* ResourceQuota
* LimitRange
* Monitoring

---

## 🔥 Scenario 8: Node Failure

**Q:**
A node goes down. What happens to pods?

**Expected behavior:**

* Node NotReady
* Pod eviction
* Rescheduling
* Impact on StatefulSets
- ### **Impact on StatefulSets if a Node Goes Down**

## ✅ Key Points

1. **Pods are tied to persistent storage**
   * Each StatefulSet pod usually has a **PersistentVolumeClaim (PVC)**
   * The data is **persistent**, even if the pod or node fails
2. **Pod scheduling depends on node availability**
   * If a node goes down, the pod on that node **cannot run**
   * Kubernetes **will try to reschedule the pod** on another node
3. **PVCs may need to be reattached**
   * Storage type matters:
     * **Cloud-managed storage (e.g., AWS EBS, GCP PD):** PVC can attach to a new node
     * **Local storage:** Pod cannot start on another node until the original node is back
4. **Startup ordering is maintained**
   * StatefulSets maintain **pod identity and order** (`pod-0`, `pod-1`, `pod-2`)
   * Pods are **recreated one by one** according to StatefulSet rules
5. **Impact summary**
   | Scenario                        | Impact                                                   |
   | ------------------------------- | -------------------------------------------------------- |
   | Node with StatefulSet pod fails | Pod becomes unavailable temporarily                      |
   | PVC is cloud-based              | Pod can restart on another node → minimal downtime       |
   | PVC is local                    | Pod cannot restart → data unavailable until node is back |

---

## 🔹 How Kubernetes self-heals StatefulSets

1. Node failure detected
2. StatefulSet controller notices pod is down
3. Tries to schedule pod on available node (if storage allows)
4. Pod starts with same name, same volume

---

### **Simple Analogy**

* **Stateless pod:** Can start anywhere → no problem
* **Stateful pod:** Needs its own “desk” (volume) → may have to wait if desk isn’t movable

---

### 🎯 One-line Interview Answer

> **If a node with a StatefulSet pod goes down, the pod becomes temporarily unavailable; Kubernetes will try to reschedule it on another node, but successful restart depends on whether the persistent volume can be reattached.**


---

## 🔥 Scenario 9: Kubernetes Upgrade

**Q:**
How do you upgrade a Kubernetes cluster safely?

**Expected steps:**

* Control plane first
* Node upgrade
* Drain nodes
* Compatibility checks

---

## 🔥 Scenario 10: Multi-Environment Strategy

**Q:**
How do you manage Dev, QA, and Prod clusters?

**Expected approaches:**

* Separate clusters vs namespaces
* GitOps
* CI/CD pipelines
* RBAC isolation

---

# 🔹 Advanced Kubernetes Questions (4+ Years Level)

### 52. What is etcd quorum?
> **Quorum in etcd is the minimum number of nodes that must agree (vote) to make a change to the cluster, ensuring consistency and preventing split-brain.**

In short:
👉 **It ensures etcd is consistent and reliable even if some nodes fail.**

---

## 🔹 How it Works

* etcd usually runs as a **cluster of odd-numbered nodes** (3, 5, 7)
* **Quorum = majority of nodes**

Example:

| Cluster size | Quorum (majority) |
| ------------ | ----------------- |
| 3 nodes      | 2 nodes           |
| 5 nodes      | 3 nodes           |
| 7 nodes      | 4 nodes           |

* **Writes and reads** require agreement from quorum
* If quorum is not reached, etcd **stops accepting writes** → prevents inconsistent data

---

## 🔹 Example

* etcd cluster: 3 nodes → quorum = 2
* Node-1 fails → 2 nodes still alive → cluster works
* Node-2 fails → only 1 node left → **quorum lost**, cluster stops writes

---

## 🔑 Key Points

* Quorum ensures **strong consistency**
* etcd clusters **must be odd-numbered** to tolerate failures
* Losing quorum = etcd **becomes read-only / unavailable**

---

### 🎯 One-Line Interview Answer

> **etcd quorum is the minimum number of etcd nodes that must agree to perform a write, ensuring data consistency and preventing split-brain scenarios.**


---

### 53. How does Kubernetes achieve self-healing?
> **Kubernetes self-healing is the ability of the cluster to automatically detect and replace failed or unhealthy pods, ensuring applications remain available.**

---

## 🔹 How It Works (Step by Step)
1. **Pod crashes or fails**
   * Example: OOMKilled, container exits with error, node goes down
2. **Controller detects failure**
   * Controllers like **Deployment, ReplicaSet, StatefulSet** constantly monitor pods
3. **Replacement happens automatically**
   * New pod is created to maintain the desired state
4. **Health checks help**
   * **Liveness probes** detect unhealthy containers → restart them
   * **Readiness probes** ensure traffic only goes to healthy pods


## 🔹 Example

* Deployment with 3 replicas
* Pod-2 crashes
* ReplicaSet notices only 2 pods are running
* Kubernetes **creates a new Pod-4 automatically**
* App stays available

---

### **Visual Flow (Simple)**

```
Pod fails → Controller detects → New pod created → Desired state restored
```

---

### 🎯 One-Line Interview Answer

> **Kubernetes achieves self-healing by automatically detecting failed pods or containers and recreating them to maintain the desired state of applications.**

---

### 54. What are Custom Resource Definitions (CRDs)?
> **A CRD is a way to tell Kubernetes: “Hey, here’s a new type of thing I want to manage, just like pods or services.”**

---

### **Think of it like this:**

* Kubernetes knows how to handle built-in resources:

  * `Pod`, `Service`, `Deployment`
* But what if you want to manage something custom, like:

  * `MySQLCluster`
  * `KafkaTopic`
  * `RedisCluster`

➡ You **define a CRD**, which teaches Kubernetes about this **new resource type**.

---

### **Example**

1. Define CRD called `MySQLCluster`
2. After that, you can do:

```yaml
apiVersion: "databases.example.com/v1"
kind: MySQLCluster
metadata:
  name: my-db
spec:
  replicas: 3
```

3. Kubernetes now understands `MySQLCluster` just like a pod or service.

4. An **Operator** can watch this resource and **automatically manage your app**.

---

### **Analogy**

* **Pods/Services** = Things Kubernetes already knows how to handle
* **CRD** = Teaching Kubernetes **a new type of thing**
* **Operator** = Someone who knows how to take care of that new thing

---

### **One-line simplified answer**

> **CRDs let you create new resource types in Kubernetes so you can manage custom applications like they were native Kubernetes objects.**

---

### 55. What is an Operator?
### **Kubernetes Operator (Simple Explanation)**

---

## ✅ Definition

> **An Operator is a custom controller that extends Kubernetes to automate the management of complex applications, including deployment, upgrades, backups, and scaling.**

In short:
👉 **It “operates” an application for you inside Kubernetes.**

---

## 🔹 How it works

1. You install an Operator for an application (e.g., MySQL, Kafka, Elasticsearch).
2. It watches **custom resources** (CRDs) in Kubernetes.
3. It performs **tasks automatically**:

   * Deploying pods
   * Configuring apps
   * Performing backups
   * Handling upgrades
4. You don’t have to manually run scripts or jobs.

---

## 🔑 Example

* **MySQL Operator**

  * You create a `MySQLCluster` custom resource
  * Operator automatically:

    * Creates pods
    * Sets up replication
    * Handles failover
    * Applies updates

---

## 🧠 Simple analogy

* Think of **Operator = Human DevOps automation inside Kubernetes**
* It knows **how to run and maintain** the app like an expert would.

---

## 🎯 One-line Interview Answer

> **An Operator is a Kubernetes controller that automates deployment and lifecycle management of complex applications using custom resources.**


---

### 56. What is Service Mesh (Istio/Linkerd)?
* A Service Mesh like Istio or Linkerd controls and secures pod-to-pod communication with proxies, providing encryption, traffic management, and monitoring.
* Another sidecar pod runs along with main pod.

---

### 57. How does Kubernetes networking differ from Docker networking?
* **Docker**

  * Containers usually talk through the **host**
  * Need **port mapping** (`-p 8080:80`)
  * Cross-host communication is **not automatic**
  * You manage networking more

* **Kubernetes**

  * Every **pod gets its own IP**
  * Pods can talk to each other **directly**, even on different nodes
  * **No port mapping** needed between pods
  * **Services + DNS** handle networking automatically

---

### **One-line difference (easy to remember)**

> **Docker networking is container + host based, Kubernetes networking is pod + cluster wide.**


---

### 58. How do you debug DNS issues in Kubernetes?
## ✅ Step 1: Check CoreDNS is running

```bash
kubectl get pods -n kube-system | grep coredns
```

Pods should be in **Running** state.

---

## ✅ Step 2: Check CoreDNS logs

```bash
kubectl logs -n kube-system -l k8s-app=kube-dns
```

Look for errors like:

* `SERVFAIL`
* `NXDOMAIN`
* Timeouts

---

## ✅ Step 3: Test DNS from inside a pod

Create a test pod:

```bash
kubectl run dns-test --image=busybox --restart=Never -- sleep 3600
```

Exec into it:

```bash
kubectl exec -it dns-test -- sh
```

Test DNS:

```sh
nslookup kubernetes.default
nslookup my-service.default.svc.cluster.local
```

---

## ✅ Step 4: Check service & endpoints

```bash
kubectl get svc my-service
kubectl get endpoints my-service
```

* Service exists ✅
* Endpoints **must not be empty**

---

## ✅ Step 5: Verify pod DNS config

Inside pod:

```sh
cat /etc/resolv.conf
```

Check:

* Correct nameserver (ClusterIP of CoreDNS)
* Correct search domains

---

## ✅ Step 6: Check Network Policies

```bash
kubectl get networkpolicy -A
```

Ensure DNS traffic (UDP/TCP 53) is allowed.

---

## ✅ Step 7: Check kube-proxy (if using ClusterIP)

```bash
kubectl get pods -n kube-system | grep kube-proxy
```

---

## Common DNS Problems & Causes

| Problem                | Likely Cause              |
| ---------------------- | ------------------------- |
| `NXDOMAIN`             | Service name wrong        |
| Timeout                | Network policy / firewall |
| No endpoints           | Pods not matching labels  |
| Works on node, not pod | DNS config issue          |

---

## 🎯 Interview one-liner

> **To debug DNS issues in Kubernetes, I check CoreDNS health and logs, test DNS resolution from inside a pod, verify services and endpoints, inspect pod DNS configuration, and ensure network policies allow DNS traffic.**

---

### 59. What is admission controller?
> **An admission controller is a Kubernetes gatekeeper that checks or modifies requests before they are saved in the cluster. Kubernetes uses built-in admission controllers like LimitRanger to enforce CPU/memory limits and ResourceQuota to prevent overuse of cluster resources.**

---

### Key clarification (important)
* ❌ You usually **do NOT create** `LimitRanger` or `ResourceQuota` as controllers
* ✅ They are **built-in admission controllers**
* You **configure them** using:
  * `LimitRange` objects
  * `ResourceQuota` objects

---

### Example (simple)
* You create a pod **without CPU/memory**
* **LimitRanger admission controller**:
  * Adds default limits
* Pod is allowed and stored
* You exceed namespace quota
* **ResourceQuota admission controller**:
  * Blocks the request

---

### One-line interview answer (polished)

> **Admission controllers are Kubernetes gatekeepers that enforce policies like resource limits and quotas by validating or modifying API requests before they are stored.**

---

### 60. What is Pod Disruption Budget (PDB)?
* Pod Disruption Budget ensures a minimum number of pods remain running during maintenance or update activities that cause voluntary disruptions.

---

# ✅ Interview Tip (Very Important)

At your experience level:

* Always explain **real incidents**
* Talk about **production traffic**
* Mention **monitoring & alerting**
* Emphasize **security & scalability**

Example answer style:

> “In our GKE/AKS/EKS cluster, we faced this issue… and solved it by…”

---

### 61. What is OOS in Kubernetes?
* OOS (Out Of Sync) means the actual state of a Kubernetes object does not match the desired state defined in manifests or in GitOps.

---

### 62. What is QoS in Kubernetes?
> **QoS (Quality of Service) in Kubernetes defines how the system prioritizes pods for scheduling and eviction based on their resource requests and limits.**

In short:
👉 **It tells Kubernetes which pods are more important when resources are tight.**

---

## 🔹 Kubernetes Pod QoS Classes

| QoS Class      | Requirements                                  | Eviction Priority   |
| -------------- | --------------------------------------------- | ------------------- |
| **Guaranteed** | Requests = Limits for **all containers**      | Last to be evicted  |
| **Burstable**  | Requests and/or limits set, but **not equal** | Middle priority     |
| **BestEffort** | No requests or limits set                     | First to be evicted |

---

## 🔹 How it works

1. Pod specifies **resource requests** (minimum) and **limits** (maximum)
2. Kubernetes calculates **QoS class** based on these values
3. During **node pressure**:

   * BestEffort pods are evicted first
   * Burstable pods next
   * Guaranteed pods last

---

### **Simple Analogy**

* **Guaranteed** → VIP pods → protected first
* **Burstable** → Normal pods → okay to evict if needed
* **BestEffort** → Low priority → evicted first

---

### 🎯 One-line Interview Answer

> **QoS in Kubernetes is a classification of pods based on resource requests and limits that determines their priority for scheduling and eviction under resource pressure.**

---

| **Pod State**           | **What it Means**                         | **Primary Root Cause Category**  | **Common Root Causes**                                                                             |
| ----------------------- | ----------------------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Pending**             | Pod created but not scheduled             | **Resources / Configuration**    | Insufficient CPU/memory, PVC not bound, nodeSelector/affinity mismatch, taints without tolerations |
| **CrashLoopBackOff**    | Container keeps crashing and restarting   | **Configuration / Dependencies** | App crash, wrong command/entrypoint, missing env vars/secrets, dependency (DB/API) unavailable     |
| **ImagePullBackOff**    | Image pull failed repeatedly              | **Configuration / Network**      | Wrong image name/tag, private registry auth missing, DNS or network issue                          |
| **ErrImagePull**        | Image pull failed (initial attempt)       | **Configuration**                | Invalid image reference, registry unavailable                                                      |
| **OOMKilled**           | Pod killed due to memory limit            | **Resources**                    | Memory limit too low, memory leak in application                                                   |
| **Running (Not Ready)** | Container running but not serving traffic | **Probes / Dependencies**        | Readiness probe failing, wrong probe path/port, downstream service unavailable                     |
| **Evicted**             | Pod removed by kubelet                    | **Resources**                    | Node memory/disk pressure, disk full                                                               |
| **Completed**           | Pod finished execution                    | **Expected (Jobs)**              | Job/CronJob completed successfully                                                                 |
| **Terminating (Stuck)** | Pod not deleting                          | **Configuration**                | Finalizers present, app not handling SIGTERM, volume unmount issues                                |
| **Error**               | **missing Configuration**/**db connection failure**                | may db credentials changes                                |
| **ContainerStatusUnknown** |      Kubernetes lost communication with the node where this pod was running.
This is a node-level problem, not an application problem.                      |


