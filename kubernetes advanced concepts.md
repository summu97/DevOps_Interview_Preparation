**Governance**: Making sure Azure resources are **used correctly and securely** using **policies, RBAC, tags, and management groups**.


# 🔵 Azure services a DevOps engineer should master

I’ll group this **the same way interviews + real projects expect it**.

---

## 1️⃣ Core Azure fundamentals (non-negotiable)

### Identity & access

* **Azure Active Directory (Microsoft Entra ID)**

  * Users, groups: Users are individual human identities, and groups are collections of users used to assign permissions together.
  * Service principals: A service principal is an application identity used by external apps, tools, or automation to access Azure resources.
  >
  > An application identity created in Entra ID
  > Used by external apps, scripts, CI/CD tools (Jenkins, GitHub Actions)
  > Requires credentials (client secret or certificate)
  > You must manage and rotate secrets
  > Can be used outside Azure
  > 
  * Managed identities (system & user-assigned): A managed identity is an Azure-managed service identity that allows one Azure service to securely access another without credentials.
  * RBAC (role assignments, scope): RBAC defines what actions an identity can perform on Azure resources and at what scope.

👉 This is HUGE in Azure interviews.

---

### Resource management

* **Subscriptions**: Acts as a billing + access boundary where resources are created and costs are tracked.
* **Resource Groups**: A logical grouping of related resources for easier management, deployment, and deletion.
* Azure Resource Manager (ARM basics)
* Tags & governance: Tags help organize and track resources, while governance ensures cost control, compliance, and policy enforcement.

---

## 2️⃣ Networking (very important in Azure)

Azure networking is a **big differentiator**.

### Must-know services

* **Virtual Network (VNet)**
* Subnets
* **NSG (Network Security Groups)**: Acts as a stateful firewall to allow or deny traffic at subnet or NIC level using rules.
* **Azure Firewall**: Azure Firewall: A centralized, fully managed network firewall for controlling inbound and outbound traffic across VNets.
* **Route Tables (UDR)**: Used to control traffic flow by defining custom routes instead of Azure’s default routing.
* **VNet Peering**
* **Private Endpoints**: Used to access Azure services privately over a private IP created in your VNet, so traffic never goes over the public internet.
* **Service Endpoints**: Service endpoints allow Azure services with public endpoints to be accessed only from specific VNets.
> Access is restricted to your VNet
> 
> Some Azure services like Storage or SQL have public endpoints; service endpoints allow resources inside selected VNets (VMs, AKS, containers, etc.) to access those services securely, while restricting access to only those VNets.

### Difference between NSG and Firewall:
> **NSGs** are used for **basic traffic control at subnet or NIC level**,
> whereas **Azure Firewall** is a **centralized, fully managed traffic controller** with **advanced capabilities like domain/URL (FQDN) whitelisting and blacklisting**.

### Connectivity

* **VPN Gateway**
* **ExpressRoute** (conceptual)
* Bastion

---

### VPN Gateway

Provides **encrypted connectivity** between Azure VNets and **on-premises or other VNets** over the **public internet**.
Supports:
* Site-to-Site
* Point-to-Site
* VNet-to-VNet

---

### ExpressRoute

Provides a **private, dedicated connection** between on-premises networks and Azure **without using the public internet**.

* Uses a **private circuit**
* More **stable, high bandwidth, low latency**

---

### Azure Bastion

Allows **secure RDP/SSH access to Azure VMs directly from the Azure Portal** without exposing **public IP addresses**.

---
* *“Which is more secure?”*

> ExpressRoute uses a private, dedicated connection and does not traverse the public internet. So it is more secure.
> VPN Gateway encrypts traffic but still travels over the public internet.

---



### 🌐 **VNet Connectivity** (broad term)

Yes — this includes **ALL ways a VNet can connect** to something else:

* **Point-to-Site (P2S)** → User / laptop → VNet
* **Site-to-Site (S2S)** → On-prem → VNet
* **VNet-to-VNet (VPN)** → VNet ↔ VNet using gateway
* **VNet Peering** → VNet ↔ VNet directly

👉 So **P2S and S2S are VNet connectivity types** ✅

---

## 🔗 **VNet Peering** (specific case)

* This is **direct VNet-to-VNet connectivity**
* Uses **Azure backbone**
* No VPN gateway
* High performance

So yes 👇

> **Direct VNet-to-VNet connectivity = VNet Peering**

---

## 🧠 Clean mental model (remember this)

```
VNet Connectivity
│
├── Point-to-Site (user → VNet)
├── Site-to-Site (on-prem → VNet)
├── VNet-to-VNet (VPN gateway)
└── VNet Peering (direct, backbone)
```

---

## 🆚 One-line clarification (important)

❌ Not fully correct:

> “VNet-to-VNet direct connectivity as peering”

✅ Correct:

> **VNet Peering is direct VNet-to-VNet connectivity without a VPN gateway**

---


## 3️⃣ Compute & containers (DevOps core)

### Virtual machines

* **Azure Virtual Machines**: follow this link for more theory **https://github.com/summu97/Azure/blob/main/3.%20VM/VM.md**
* VM Scale Sets (VMSS): VM Scale Set (VMSS) is a managed group (cluster) of identical virtual machines that can scale in or out automatically.
* Custom images: Pre-configured VM images used to create multiple identical VMs with required software and settings.
* Availability Sets & Zones: Ensure high availability by distributing VMs across separate fault/update domains (sets) or physically isolated datacenters (zones).

NOTE: Go through this **https://github.com/summu97/Azure/blob/main/3.%20VM/VM.md**
---

### Containers & Kubernetes

* **Azure Kubernetes Service (AKS)** (VERY IMPORTANT)

  * Node pools
  * Autoscaling
  * RBAC with AAD
  * Ingress (NGINX / AGIC)
  * Upgrades
* **Azure Container Registry (ACR)**

  * Image scanning: Automatically detects vulnerabilities in container images stored in ACR.
  > ACR supports image vulnerability scanning through Microsoft Defender for Cloud, not natively by itself.
  * Geo-replication: Replicates container images to multiple Azure regions for low-latency access and high availability.

---

## 4️⃣ CI/CD & DevOps tooling (you’ll shine here)

### Azure DevOps

* **Pipelines (YAML)**
* Repos
* Artifacts
* Service connections
* Environments & approvals

### GitHub in Azure context

* GitHub Actions with Azure
* OIDC (no secrets)
* Deploy to AKS / App Services

---

## 5️⃣ Storage (you will use this a lot)

* **Azure Storage Account**

  * Blob
  * File Share
  * Queue
* Storage redundancy (LRS, ZRS, GRS)
Got it — here’s the **clear + detailed version** interviewers love 👌

### Azure Storage Redundancy (with copies & locations)

* **LRS (Locally Redundant Storage)** → same datacenter
  Stores **3 copies** of data **within a single datacenter** in one region.

* **ZRS (Zone-Redundant Storage)** → different zones
  Stores **3 copies** of data **across 3 different availability zones** in the same region.

* **GRS (Geo-Redundant Storage)** → different regions
  Stores **6 copies total**:
  → **3 copies** in the primary region
  → **3 copies** in a paired secondary region (same datacenter level).

* **GZRS (Geo-Zone-Redundant Storage)** → zones + regions (maximum protection)
  Stores **6 copies total**:
  → **3 copies across availability zones** in the primary region
  → **3 copies** in a secondary paired region.

---

* Lifecycle policies:

### What are Azure Storage Lifecycle Policies?

**Lifecycle policies** automatically **move or delete data** based on **rules and age** to reduce storage costs.

### What can they do?

* Move blobs from:

  * **Hot → Cool**
  * **Cool → Archive**
* **Delete blobs** after a defined number of days

### Single-liner (interview-ready)

> **Lifecycle policies automate data tiering and deletion in Azure Storage based on access patterns and age to optimize costs.**

### Example (real-world)

* Logs:

  * Hot for 30 days
  * Cool for 90 days
  * Archive after 180 days
  * Delete after 1 year



* SAS vs Managed Identity access
---
### How many ways i can access storage blobs
> Azure Storage can be accessed using Access Keys, SAS tokens, Azure AD RBAC (including Managed Identity), or public access.

### Access Keys

✔️ Give **full access to the entire storage account**
✔️ **Least secure** (keys = full control, hard to rotate)

---

### SAS Tokens(Shared Access Signature)

✔️ Give **limited access** at **container / blob / file level**
✔️ Restricted by **time, permissions, and IP (optional)**
✔️ Safer than access keys

---

### RBAC (Azure AD)

⚠️ Small correction here 👇
✔️ RBAC gives **management-plane + data-plane access**
✔️ Users don’t *just* need portal access — they need:

* Azure AD identity
* Proper **storage data roles** (Blob Reader, Contributor, etc.)

---

### Managed Identity + RBAC

✔️ Uses **Azure AD RBAC**, same roles as users
✔️ **No secrets or keys**
✔️ Best for **Azure service-to-service access**

---

## 6️⃣ Security & secrets (very important)

### Secrets management

* **Azure Key Vault**

  * Secrets, keys, certificates
  * Access via Managed Identity
  * Integration with AKS, pipelines

---

### Security posture

* Microsoft Defender for Cloud
* Azure Policy
* Role assignments best practices

---
### What is NIC?
* NIC is a network interface card that acts as the entry and exit point for all network traffic of a VM, and NSG rules can be applied at the NIC or subnet level.

Nice — this is **almost right** 👍
Just a small polish to make it **technically perfect and interview-ready**:

---

### What is NSG?

* **Network Security Group (NSG)** is used to **allow or deny inbound and outbound network traffic** to **subnets or VM NICs**.

### What is ASG?

* **Application Security Group (ASG)** is used to **logically group VM NICs** so that **NSG rules can be applied using those groups instead of IP addresses**.
* Basically NSG is what actually allows or denies traffic, and ASG is used to logically group VMs so NSG rules can be applied easily.

---
################################################################################################
## 7️⃣ Load balancing & traffic management

### Must-know

* **Azure Load Balancer**
* **Application Gateway**
* **Application Gateway + WAF**
* **Azure Front Door**
* **Azure Traffic Manager**

👉 Interviews love asking:
**“App Gateway vs Front Door”**

In Azure, there are **4 main types of load balancers**, depending on layer and use case 👇

### 1️⃣ **Azure Load Balancer (Layer 4)**

* Works at **TCP/UDP level**
* Distributes traffic based on **IP and port**
* Used for **VMs / VM Scale Sets**
* Supports **Public & Internal** load balancing

**One-liner**: L4 load balancer for high-performance network traffic.

---

### 2️⃣ **Application Gateway (Layer 7)**

* Works at **HTTP/HTTPS level**
* Supports **URL-based routing, SSL termination, cookies**
* Integrates with **WAF**

**One-liner**: L7 load balancer for web applications.

---
Front Door → for global, multi-region applications
Application Gateway → for regional, single-region apps or internal apps

Sure! Here’s a **concise 5-point comparison** you can easily remember:

---

### **Azure Application Gateway**

1. Layer 7 (Application Layer), **regional** only
2. Supports **path-based & host-based routing**
3. Routes traffic to **VMs, VMSS, or App Services** in the same region
4. Optional **WAF** for web apps
5. Best for **single-region web apps** and internal/external apps

---

### **Azure Front Door**

1. Layer 7, **global edge service**
2. Supports **host/path-based routing, geo-routing, and global load balancing**
3. Routes traffic to **any publicly addressable backend**, including regional App Gateways
4. Optional **WAF**, SSL termination, and CDN caching
5. Best for **multi-region global apps** with low latency and failover

---

If you want, I can also make an **even simpler 2-line “interview-ready” version** that nails the difference in one glance.

Do you want me to do that?


---
### 3️⃣ **Azure Front Door (Global, Layer 7)**

* Global HTTP/HTTPS load balancer
* Routes traffic to **nearest healthy region**
* Provides **CDN, SSL, WAF**

**One-liner**: Global L7 load balancer for multi-region apps.

---
🔹 Azure Firewall vs WAF
| Feature              | Azure Firewall                     | Azure WAF (Web Application Firewall)                  |
| -------------------- | ---------------------------------- | ----------------------------------------------------- |
| **OSI Layer**        | L3 / L4 / L7                       | **L7 (Application Layer)**                            |
| **What it protects** | Network (IP, ports, protocols)     | **Web applications (HTTP/S traffic)**                 |
| **Use case**         | Block unwanted IPs, ports, subnets | Block attacks like SQL injection, XSS, malicious bots |
| **Traffic type**     | All traffic (VNet, Internet)       | Only HTTP/HTTPS traffic                               |
| **Rules**            | IP/Port rules, Network rules, NAT  | OWASP rules, custom app rules                         |
| **Example**          | Block 10.1.0.0/16, allow port 443  | Block `/login` attempts with SQL injection patterns   |

---

### 4️⃣ **Traffic Manager (DNS-based)**

* Uses **DNS routing**
* Routes users based on **performance, priority, or geography**

**One-liner**: DNS-based traffic routing across regions.

---

### Super-short interview summary 🔥

> Azure provides **Load Balancer (L4), Application Gateway (L7), Front Door (global L7), and Traffic Manager (DNS-based)**.

If you want, I can also give **when to use which** in one table — very common follow-up 😊
########################################################################################

---

## 8️⃣ Monitoring & logging (production skills)

* **Azure Monitor**
* **Log Analytics Workspace**
* **Application Insights**
* Alerts (metric & log based)
* AKS monitoring

---

## 9️⃣ Infrastructure as Code (Azure-specific)

You already know Terraform — now master **Azure flavor**:

* AzureRM provider
* State in **Azure Storage backend**
* Remote state & data sources
* Modules & environments
* Terraform + Azure DevOps pipeline

---

## 10️⃣ Governance & enterprise features (to move senior)

* **Azure Policy**
* Management Groups
* Cost Management
* Budgets & alerts
* Blueprints (basic understanding)

---

# 🧠 Priority order (very important)

If you want a **clean learning path**, follow this order:

### Phase 1 (must be rock solid)

1. Entra ID + RBAC
2. VNet + NSG + Private Endpoints
3. AKS + ACR
4. Storage Account
5. Key Vault

### Phase 2 (real DevOps work)

6. Azure DevOps / GitHub Actions
7. App Gateway + Front Door
8. Monitoring & Alerts
9. Terraform with Azure backend

### Phase 3 (senior level)

10. Azure Policy
11. Defender for Cloud
12. Cost Management

---



