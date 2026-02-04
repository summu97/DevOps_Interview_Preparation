# 🔵 Azure services a DevOps engineer should master

I’ll group this **the same way interviews + real projects expect it**.

---

## 1️⃣ Core Azure fundamentals (non-negotiable)

### Identity & access

* **Azure Active Directory (Microsoft Entra ID)**

  * Users, groups: Users are individual human identities, and groups are collections of users used to assign permissions together.
  * Service principals: A service principal is an application identity used by external apps, tools, or automation to access Azure resources.
  > An application identity created in Entra ID
  > 
  > Used by external apps, scripts, CI/CD tools (Jenkins, GitHub Actions)
  > 
  > Requires credentials (client secret or certificate)
  > 
  > You must manage and rotate secrets
  > 
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

### Difference between NSg and Firewall:
> **NSGs** are used for **basic traffic control at subnet or NIC level**,
> whereas **Azure Firewall** is a **centralized, fully managed traffic controller** with **advanced capabilities like domain/URL (FQDN) whitelisting and blacklisting**.

### Connectivity

* **VPN Gateway**
* **ExpressRoute** (conceptual)
* Bastion

---

## 3️⃣ Compute & containers (DevOps core)

### Virtual machines

* **Azure Virtual Machines**
* VM Scale Sets (VMSS)
* Custom images
* Availability Sets & Zones

---

### Containers & Kubernetes

* **Azure Kubernetes Service (AKS)** (VERY IMPORTANT)

  * Node pools
  * Autoscaling
  * RBAC with AAD
  * Ingress (NGINX / AGIC)
  * Upgrades
* **Azure Container Registry (ACR)**

  * Image scanning
  * Geo-replication

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
* Lifecycle policies
* SAS vs Managed Identity access

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

## 7️⃣ Load balancing & traffic management

### Must-know

* **Azure Load Balancer**
* **Application Gateway**
* **Application Gateway + WAF**
* **Azure Front Door**
* **Azure Traffic Manager**

👉 Interviews love asking:
**“App Gateway vs Front Door”**

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

# 🎯 What companies expect from someone like you

With your skills, recruiters expect:

* AKS + CI/CD + Terraform
* Secure access using Managed Identity
* Private networking
* Production monitoring
* Cost & security awareness


