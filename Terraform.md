# The recommended Terraform lifecycle

```bash
terraform fmt → terraform init → terraform validate → terraform plan → terraform apply.
```
---

### 1. What **terraform fmt** do?
* It Fixes indentation, Aligns arguments, Standardizes spacing

---

### 2. What **terraform init** do?
* It Initializes the working directory, Downloads providers, Configures backend

---

### 3. What **terraform validate** do?
* It validates Terraform configuration files for Syntax errors, Invalid argument names, Type mismatches, Missing required variables
* Ensures modules and providers are valid (after init)

---

### 4. What **terraform plan** do?
It Creates an execution plan.
- It Compares:
  * Terraform configuration
  * Terraform state
  * Real infrastructure
And Shows:
  * What resources will be **created**
  * What will be **updated**
  * What will be **destroyed**

---

### 5. What **terraform apply** do?
It Creates / updates / deletes real resources
* **After successful execution**, Terraform:
  * Creates **terraform.tfstate** (local backend)
  * OR updates remote state (Azure Storage, S3, GCS, etc.)

#### When is the `.tfstate` file created?
* The **`.tfstate` file is created during `terraform apply`**

---

### 6. What **terraform plan -out=tfplan** do?
* Saves the execution plan to a file
* Used for approval-based pipelines

---

### 7. What **terraform refresh** do?
* It Updates state from real infrastructure
* Requires state to already exist

---

# Terraform Internal Files

---

## 1️⃣ `.terraform/` Directory

### ✅ When is `.terraform/` created?

👉 During: terraform init

---

### 📂 What is inside `.terraform/`?

**Key contents:**

* Downloaded providers
* Downloaded modules
* Backend initialization data
* Provider binaries (OS-specific)

**Important notes:**

* Environment-specific
* Safe to delete and re-create
* Must be added to `.gitignore`

---

## 2️⃣ `.terraform.lock.hcl`

### ✅ When is `.terraform.lock.hcl` created?

👉 During `terraform init`

Specifically when:

* A provider is downloaded for the first time
* Provider versions change

---

### 📄 What is inside `.terraform.lock.hcl`?

Example:

```hcl
provider "registry.terraform.io/hashicorp/azurerm" {
  version     = "3.70.0"
  constraints = "~* 3.0"
  hashes = [
    "h1:abc123...",
    "zh:xyz456..."
  ]
}
```

---

### 🔹 Contains:

* Exact provider versions
* Provider source registry
* Checksum hashes for integrity verification

---

### 📌 Git Best Practices

| File                  | Commit to Git             |
| --------------------- | ------------------------- |
| `.terraform/`         | ❌ No                      |
| `.terraform.lock.hcl` | ✅ Yes                     |
| `.tfstate`            | ❌ No (use remote backend) |

---

### 8. How to unlock terraform lock?
* terraform force-unlock LOCK_ID

## What problems occur if the state file is lost or corrupted?
- a. Terraform “forgets” what it manages
* If the state file is missing or corrupted, Terraform no longer knows which resources it has already created. So when you run terraform plan or apply, Terraform may try to re-create resources that already exist, because it thinks they don’t exist yet. This can lead to:
* Duplicate infrastructure
* Conflicts with unique resource names
* Errors from the cloud provider

### 9. How does **state locking** work?
* Terraform state locking prevents multiple users or pipelines from modifying the same state file at the same time.
* Terraform automatically acquires a lock before updating state and releases it after the operation, preventing state corruption and concurrent writes.

### 10. What happens if two users run `terraform apply` at the same time?
* If two users run terraform apply at the same time, Terraform state locking allows only one operation to proceed.
* The second user’s apply will fail or wait until the state lock is released, preventing state corruption and conflicting changes.

### 11. Explain `terraform state list`, `mv`, `rm`, and `pull`.
🔹 **terraform state list**
* Lists all resources currently tracked in the Terraform state file.
* Used to see what Terraform is managing in the current workspace.

🔹 **terraform state mv**
* Moves or renames a resource inside the state file only.
* Used when refactoring code (renaming resources or moving them into modules) without recreating infrastructure.

🔹 **terraform state rm**
* Removes a resource from the state file only, not from real infrastructure.
* Terraform will stop managing that resource, but it will continue to exist in the cloud.

🔹 **terraform state pull**
* Downloads and displays the current state file from the backend.
* Used for backup, inspection, or debugging of remote state.

---

### 12. Can you use multiple providers in one Terraform project?
* Yes ✅ — you can use multiple providers in a single Terraform project.
* Terraform allows defining multiple providers (even multiple configurations of the same provider) and assigning them to different resources or modules using provider aliases.

### 13. What happens if a provider version changes?
* If a provider version changes, Terraform will download the new version during terraform init and update .terraform.lock.hcl.
* Depending on the change, it may introduce new features, behavior changes, or breaking changes, which can affect plan and apply, so provider upgrades should be tested carefully.
---


### 14. Difference between **terraform.tfvars** and **.auto.tfvars**
* .tfvars files: Only terraform.tfvars is auto-loaded. Other files like dev.tfvars or qa.tfvars must be passed explicitly using -var-file.
* .auto.tfvars files: All files ending with .auto.tfvars in the current directory (like dev.auto.tfvars, qa.auto.tfvars) are automatically loaded and merged.


---

### 15. Difference between **Root module** and **Child module**
* main.tf (or the files in your project root directory) is the root module.
* Any folder you create like modules/vnet, modules/vm, etc., which is called from the root module using the module block, is a child module.
* Root module = entry point, Child module = reusable component called by root.

### 16. How do you version Terraform modules?
* “Modules are versioned using either the Terraform Registry version argument, Git tags with ref, or branches; using tags or registry versions is best for stable, reproducible deployments.”

- **Terraform Registry:**

```bash
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.15.0"
}
```

Locks the module to a specific release.

- **Git repository with tags:**

```bash
module "app" {
  source = "git::https://github.com/myorg/terraform-modules.git//app?ref=v1.2.0"
}
```

Uses a Git tag to fix the module version.
- Avoid using branches for production because they can change unexpectedly.


### 17. What are **module inputs and outputs**?
- **Module Inputs:**
  * Definition: Variables that you pass into a module to configure it.
  * Purpose: Make modules reusable and flexible.

Example:
# modules/app/variables.tf
```bash
variable "instance_type" {
  type    = string
  default = "t2.micro"
}

# modules/app/main.tf
resource "aws_instance" "app" {
  ami           = "ami-123456"
  instance_type = var.instance_type
}
```

- **Module Outputs:**
  * Definition: Values that a module exports to the parent module or root configuration.
  * Purpose: Share important information like resource IDs, IPs, or ARNs.

Example:
```bash
# modules/app/outputs.tf
output "instance_id" {
  value = aws_instance.app.id
}

# root/main.tf
output "app_instance_id" {
  value = module.app.instance_id
}
```


### 18. When should you not use modules?
- For very small or simple configurations
- If your Terraform code is only a few resources, modules add unnecessary complexity.
- When you don’t need reusability
- Modules are mainly for reusable code. If resources are one-off and not shared, a module may be overkill.
- For highly dynamic or unique resources
- If resources vary greatly and can’t be parameterized easily, creating a module can make the code harder to maintain.

---


### 19. Difference between **count** and **for_each**
- "count is for multiple identical resources; for_each is for multiple resources with different attributes or keys."

### 20. Explain **dynamic blocks**.
### 21. What are **Terraform functions**? Name a few you’ve used.

### 22. What is the **lifecycle block**?
### 23. Explain:
* `create_before_destroy`
* `prevent_destroy`
* `ignore_changes`
* `replace_triggered_by`

---


### 24. Difference between **Workspaces** and **Separate state files**
- “Workspaces are logical partitions of a single backend with limited isolation, while separate state files/backends give full isolation and are safer for production environments.”

### 25. Do you recommend workspaces for prod? Why or why not?
- Workspaces are fine for dev or staging, but not recommended for production because they share the same backend and increase risk of accidental changes. Use separate backends/configurations for production instead.
1️⃣ Applying to the wrong workspace
Example: You meant to apply changes in dev but are currently in prod workspace.
```bash
terraform workspace select prod
terraform apply
```

2️⃣ Sharing backend between multiple environments
Workspaces share the same backend (e.g., S3 bucket).
Accidentally pointing prod workspace to a backend containing dev state can overwrite production state.

Someone might by mistake change prod state etc.....

---

### 26. How do you scan Terraform code for security issues?
- “Scan Terraform code for security issues using tools like tfsec, Checkov, or terrascan, and integrate them into CI/CD pipelines to catch misconfigurations before deployment.”
| Tool          | Description                                                              |
| ------------- | ------------------------------------------------------------------------ |
| **tfsec**     | Scans Terraform code for security misconfigurations (AWS, Azure, GCP).   |
| **Checkov**   | CI/CD friendly tool to detect cloud security misconfigurations.          |
| **terrascan** | Scans IaC templates against compliance rules and best practices.         |
| **KICS**      | Detects misconfigurations in Terraform, CloudFormation, Kubernetes, etc. |

EX: 
# Scan Terraform files in current directory
```bash
tfsec .
```

### 27. What is **Terraform Drift**?
- “Terraform Drift happens when real infrastructure changes outside Terraform, causing the state file and actual resources to mismatch; it can be detected with terraform plan and fixed with terraform apply or state updates.”
---

# 🔹 Scenario-Based Terraform Interview Questions (VERY IMPORTANT)

These are **must-prepare** for a 4+ year engineer.

---

## 🔥 Scenario 1: State Conflict

**Q:**
Two engineers ran `terraform apply` at the same time and state got corrupted.
👉 How would you prevent this and how would you fix it?

**Expected areas:**

* Remote backend
* State locking (S3 + DynamoDB / Azure Blob + Lease)
* `terraform state pull`
* State recovery strategy
- “To prevent state corruption, always use a remote backend with state locking (S3 + DynamoDB or Azure Blob + Lease). If state is corrupted, pull the state with terraform state pull, restore from backup, or repair manually using state commands before re-applying.”

```bash
terraform state pull * state_backup.tf
```
Pulls the current state into a local backup file.

- Restore from backup
  * If you have a recent backup of your state, restore it to the remote backend.
- Manually repair state (if needed)

Use commands like:
```bash
terraform state list        # See resources in state
terraform state rm <res*   # Remove corrupted entries
terraform import <res* ... # Re-import real resource into state
```

- Re-run terraform plan and apply
- After restoring or fixing the state, validate with a plan before applying.
---
## 🔥 Scenario 2: Resource Accidentally Deleted

**Q:**
A resource was manually deleted from the cloud console. What happens during the next `terraform apply`?

**Follow-ups:**

* How do you detect drift?
* How do you re-create vs ignore the resource?

---

### ❓ Scenario: Resource manually deleted from the cloud console

If a resource managed by Terraform is **manually deleted** outside Terraform (e.g., via AWS/Azure console), then:

* During the next `terraform plan` or `terraform apply`, Terraform will **detect that the resource is missing**.
* Terraform will plan to **re-create the resource** to match the declared configuration in the `.tf` files.

---

### ❓ How do you detect drift?

* **Terraform automatically detects drift** by comparing the **Terraform state** with the **real infrastructure**.
* Commands to detect drift:

```bash
terraform plan
# Shows resources that are missing, changed, or out-of-sync
```

```bash
terraform refresh
# Updates the state file to match real infrastructure without making changes
```

* Example output:

```
# aws_instance.web has been deleted outside Terraform
-/+ destroy and create replacement
```

---

### ❓ How do you re-create vs ignore the resource?

1. **Re-create the resource (default behavior):**

   * Simply run:

   ```bash
   terraform apply
   ```

   * Terraform will recreate the deleted resource to match the configuration.

2. **Ignore the resource:**

   * Use the `lifecycle` block with `ignore_changes` or `prevent_destroy`:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  lifecycle {
    prevent_destroy = true     # Terraform will fail if it tries to destroy
    ignore_changes  = [tags]  # Ignore specific attributes if changed outside
  }
}
```

* If the resource is deleted but you want Terraform to **ignore it completely**, you can also **remove it from the state**:

```bash
terraform state rm aws_instance.web
```

* Terraform will stop managing it and **won’t try to re-create it**.

---

### ✅ Interview One-liner

* “If a resource is manually deleted, Terraform detects it as drift and plans to re-create it. You can detect drift with `terraform plan` or `terraform refresh`, re-create it with `terraform apply`, or ignore it using `lifecycle` blocks or removing it from the state.”


---

## 🔥 Scenario 3: Renaming a Resource

**Q:**
You renamed a Terraform resource block and now Terraform wants to destroy and recreate it.
👉 How do you avoid downtime?

**Expected answer:**

* `terraform state mv` : # Move state so Terraform knows it's the same resource
terraform state mv aws_instance.web_old aws_instance.web_new

* Resource mapping
When moving resources between modules or workspaces, you can also map old state to new module paths: terraform state mv aws_instance.web_new module.app.aws_instance.web

* Lifecycle handling
```bash
  lifecycle {
    prevent_destroy = true
  }
```
---

## 🔥 Scenario 4: Multiple Environments (Dev / QA / Prod)

**Q:**
How would you design Terraform for **multiple environments**?

**Expected approaches:**

* Separate state per environment
* Backend config
* Folder structure
* CI/CD driven environment selection

---

## 🔥 Scenario 5: Zero-Downtime Deployment

**Q:**
You need to update a VM or Load Balancer without downtime.
👉 How do you handle this in Terraform?

**Expected topics:**

* `create_before_destroy`
* Blue-Green / Rolling strategies
* Integration with autoscaling groups / MIGs / VMSS

---

### ❓ Can you implement Blue-Green or Rolling deployment strategies in Terraform?

**Answer:**
Yes, you can, but Terraform **does not have built-in deployment strategies**. You achieve them by structuring your infrastructure and updates carefully, often combined with CI/CD tools or load balancers.

---

### ❓ How can you implement Blue-Green deployments in Terraform?

**Answer:**

* Maintain **two sets of resources** (Blue and Green).
* Use **DNS or load balancer** to route traffic to one environment.
* Deploy new versions to the idle environment, then **switch traffic**.

**Example:**

```hcl
module "app_blue" {
  source  = "./app"
  version = "1.0"
}

module "app_green" {
  source  = "./app"
  version = "2.0"
}
```

---

### ❓ How can you implement Rolling deployments in Terraform?

**Answer:**

* Update resources **gradually in batches** using `count`, `for_each`, or `-target`.
* Apply changes incrementally to subsets of resources.

**Example:**

```bash
terraform apply -target=module.app[0]
terraform apply -target=module.app[1]
```

---

### ❓ Key Notes

**Answer:**

* Terraform handles **infrastructure provisioning**, not traffic shifting.
* CI/CD tools or load balancers are used for traffic management.
* Modules, workspaces, and state targeting help safely apply updates gradually.

---


## 🔥 Scenario 6: Terraform in CI/CD

**Q:**
How do you run Terraform in a CI/CD pipeline securely?

**Expected points:**

* Remote backend
* Service principals / IAM roles
* `terraform plan` approval
* Separate apply stage
* No local state

---

## 🔥 Scenario 7: Module Design

**Q:**
You are asked to create a reusable VPC module.
👉 What inputs, outputs, and validations would you add?

**Expected areas:**

* Variables with validation
* Outputs for IDs
* Versioning
* Readme & examples

---

## 🔥 Scenario 8: Breaking Change in Provider

**Q:**
After upgrading a provider version, Terraform plan shows massive changes.
👉 What steps do you take?

**Expected answer:**

* Provider version pinning
* Changelog review
* `terraform init -upgrade`
* Testing in lower environments

---

## 🔥 Scenario 9: Large Terraform Codebase

**Q:**
Your Terraform apply takes 45 minutes. How do you optimize it?

**Expected ideas:**

* Split states
* Reduce dependencies
* Parallelism
* Targeted applies (carefully)

Here’s the **question and answer format** for Terraform parallelism:

---

### ❓ Does Terraform support parallelism?

**Answer:**
Yes, Terraform supports **parallelism**, which allows multiple resources to be created, updated, or deleted at the same time, speeding up deployments.

---

### ❓ How do you control parallelism in Terraform?

**Answer:**
Use the `-parallelism` flag with `terraform apply` or `terraform plan`:

```bash
terraform apply -parallelism=10
```

* Default is 10.
* Terraform will still respect **resource dependencies**.

---

### ❓ Why use or limit parallelism?

**Answer:**

* **Use it** to speed up deployment of independent resources.
* **Limit it** to avoid API rate limits or conflicts when deploying many resources simultaneously.

---

**Interview one-liner:**

* “Terraform supports parallelism to deploy multiple resources concurrently, controlled via `-parallelism`, while respecting dependencies to ensure safe execution.”

---

## 🔥 Scenario 10: Secrets Handling

**Q:**
How do you pass database passwords to Terraform securely?

**Expected solutions:**

* Vault
* Azure Key Vault / AWS Secrets Manager / GCP Secret Manager
* Environment variables
* Avoid plaintext tfvars

### How do you refactor Terraform without downtime?
* Refactor simply means restructuring or reorganizing your code/configuration without changing its behavior.
* Moving resources from main.tf into a module
* Renaming variables or resources for clarity
* Splitting a large file into smaller, logical files

### ❓ How to Refactor Terraform Without Downtime
* Move resources in state first – use terraform state mv so Terraform knows resources already exist.
* Don’t recreate resources – rename or reorganize carefully.
* Use modules safely – move resources into modules gradually.
* Test in dev/staging – never apply big changes directly to production.
* Plan first – run terraform plan to check changes.
* Apply step by step – make small incremental changes.
* Optional: use -target to apply specific resources first.

**Interview one-liner:**
“Refactor Terraform by moving resources in state, testing in dev, planning carefully, and applying changes gradually to avoid downtime.”

---

### How do you manage **cross-account / cross-subscription** deployments?
Use provider aliasing to define multiple accounts/subscriptions and assign the correct provider to each resource or module.

