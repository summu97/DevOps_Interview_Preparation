# *Azure DevOps*

Let’s break down **Azure DevOps YAML pipeline structure** in simple terms — this is crucial for understanding how pipelines are built.

---

## **1️⃣ Stages**

* **Definition:** Highest-level unit in a pipeline.
* **Purpose:** Groups **jobs** together, often representing a phase like **Build, Test, Deploy**.
* **Runs:** Can run **sequentially** or **in parallel**.

**Example:**

```yaml
stages:
  - stage: Build
    displayName: "Build Stage"
  - stage: Deploy
    displayName: "Deploy Stage"
```

* `Build` stage runs first, then `Deploy` stage runs after it.

---

## **2️⃣ Stage**

* **Definition:** A single stage within `stages`.
* **Contains:** One or more **jobs**.
* **Purpose:** Organizes jobs for a logical task (e.g., “Build Stage”).

**Example:**

```yaml
- stage: Deploy
  displayName: "Deploy Stage"
  jobs: 
    - job: DeployJob
      steps:
        - script: echo "Deploying app"
```

---

## **3️⃣ Jobs**

* **Definition:** A set of **steps** that run together on an agent.
* **Purpose:** Can run **in parallel with other jobs in the same stage**.
* **Note:** Each job runs on a **single agent (VM/container)**.

**Example:**

```yaml
jobs:
  - job: TestJob
    pool:
      vmImage: 'ubuntu-latest'
    steps:
      - script: echo "Running tests"
```

* You can have **multiple jobs in a stage** to do tasks like build, test, deploy **simultaneously**.

---

## **4️⃣ Job**

* **Definition:** A single job inside `jobs`.
* **Contains:** `steps`.

**Example:**

```yaml
- job: BuildJob
  steps:
    - script: echo "Building app"
```

* Runs all steps sequentially on one agent.

---

## **5️⃣ Steps**

* **Definition:** Individual tasks or scripts inside a job.
* **Purpose:** The **smallest unit of work** (e.g., run a script, build, deploy, install tools).
* **Runs:** Sequentially within the job.

**Example:**

```yaml
steps:
  - script: echo "Installing dependencies"
  - script: echo "Building Docker image"
```

* These two steps run **one after the other** in the `BuildJob`.

---

### **🔹 Visual Hierarchy**

```
Pipeline
 └─ stages
      └─ stage (Build / Deploy)
           └─ jobs
                └─ job (BuildJob / DeployJob)
                     └─ steps
                          ├─ script / task
                          ├─ script / task
```

* **Stages:** Build, Test, Deploy
* **Jobs:** Parallel tasks within a stage
* **Steps:** Sequential commands or tasks in a job

---

### **🎯 Quick Analogy**

* **Pipeline** → Whole project workflow
* **Stage** → Phase of the workflow (Build / Deploy)
* **Job** → Worker doing the work
* **Step** → Individual task that the worker performs

---


---
```bash
trigger: none   # You can trigger manually

(OR)

# runs automatically whenever a pull request is created or updated targeting main.
trigger: none   # Disable push triggers
pr:
  branches:
    include:
      - main  # Runs on PRs targeting main

(OR)

# runs only when manually triggered
trigger: none   # Disable push triggers
pr: none        # Disable PR triggers

parameters:
  - name: environment
    displayName: "Select Environment"
    type: string
    default: dev
    values:
      - dev
      - staging
      - prod
  - name: service
    displayName: "Select Service"
    type: string
    default: app1
    values:
      - app1
      - app2
      - app3

stages:
  - stage: Terraform_Deploy
    displayName: "Deploy Terraform Modules"
    jobs:
      - job: Terraform
        displayName: "Run Terraform"
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - checkout: self

          # Install Terraform
          - task: TerraformInstaller@1
            inputs:
              terraformVersion: '1.6.0'

          # Initialize Terraform
          - script: |
              cd terraform/${{ parameters.environment }}/${{ parameters.service }}
              terraform init -backend-config="env=${{ parameters.environment }}"
            displayName: "Terraform Init"

          # Plan Terraform
          - script: |
              cd terraform/${{ parameters.environment }}/${{ parameters.service }}
              terraform plan -out=tfplan
            displayName: "Terraform Plan"

          # Apply Terraform
          - script: |
              cd terraform/${{ parameters.environment }}/${{ parameters.service }}
              terraform apply -auto-approve tfplan
            displayName: "Terraform Apply"
```

# 🔹 Azure DevOps – Theoretical Interview Questions

## 1️⃣ Azure DevOps Fundamentals

1. What is Azure DevOps?
2. Azure DevOps Services vs Azure DevOps Server.
3. What are the core components of Azure DevOps?
4. How Azure DevOps fits into CI/CD?
5. Difference between Azure DevOps and Jenkins.

---

## 2️⃣ Azure Repos

6. What is Azure Repos?
7. Difference between:

   * Git repo
   * TFVC
8. What is a pull request in Azure Repos?
9. How do branch policies work?
10. How do you enforce code reviews?

---

## 3️⃣ Azure Pipelines (Core)

11. What is Azure Pipeline?
12. Difference between:

* Classic pipeline
* YAML pipeline

13. Why is YAML pipeline preferred?
14. Where is `azure-pipelines.yml` stored?
15. Explain pipeline stages, jobs, and steps.

---

## 4️⃣ YAML Pipeline Concepts

16. Basic structure of Azure DevOps YAML pipeline.
17. Difference between:

* Stage
* Job
* Step

18. What are pipeline triggers?
19. How do you define conditions?
20. How do you pass variables between stages?

---

## 5️⃣ Agents & Pools

21. What is a build agent?
22. Microsoft-hosted vs Self-hosted agents.
23. When do you use self-hosted agents?
24. How do agent pools work?
25. Can one agent run multiple jobs?

---

## 6️⃣ Variables, Parameters & Secrets

26. Difference between:

* Variables
* Parameters

27. What are variable groups?
28. How do you store secrets securely?
29. How do you mask secrets in logs?
30. Can variables be overridden at runtime?

---

## 7️⃣ Artifacts & Releases

31. What are pipeline artifacts?
32. Difference between:

* Build artifacts
* Pipeline artifacts

33. What is Azure Artifacts?
34. How do you version artifacts?
35. How do you promote artifacts between environments?

---

## 8️⃣ Environments & Approvals

36. What are environments in Azure DevOps?
37. What are approval gates?
38. Manual vs automated approvals.
39. How do you enforce approvals for prod?
40. What is deployment job?

---

## 9️⃣ Security & Access Control

41. How does Azure DevOps handle authentication?
42. What is RBAC in Azure DevOps?
43. How do you restrict pipeline execution?
44. How do you protect secrets?
45. How do you audit pipeline activity?

---

## 🔹 Scenario-Based Azure DevOps Interview Questions (VERY IMPORTANT)

---

## 🔥 Scenario 1: Pipeline Fails on Hosted Agent

**Q:**
Pipeline works locally but fails on Microsoft-hosted agent. How do you debug?

**Expected areas:**

* Agent OS differences
* Tool versions
* Logs
* Task compatibility

---

## 🔥 Scenario 2: Multi-Environment CI/CD

**Q:**
How do you design a pipeline for Dev → QA → Prod?

**Expected approach:**

* Multi-stage YAML
* Artifact reuse
* Environment approvals
* Conditions

---

## 🔥 Scenario 3: Secrets Leaked in Logs

**Q:**
A secret value is visible in pipeline logs. How do you fix and prevent it?

**Expected answer:**

* Secret variables
* Key Vault integration
* Masking
* Audit history

---

## 🔥 Scenario 4: Terraform with Azure DevOps

**Q:**
How do you run Terraform securely in Azure DevOps?

**Expected areas:**

* Service connections
* Remote backend
* Plan & apply stages
* Approval gates

---

## 🔥 Scenario 5: Agent Capacity Issue

**Q:**
Pipelines are queued for long time. How do you solve it?

**Expected solutions:**

* Increase agents
* Parallel jobs
* Self-hosted pools

---

## 🔥 Scenario 6: Conditional Deployment

**Q:**
Deploy to Prod only from main branch. How do you enforce this?

**Expected tools:**

* YAML conditions
* Branch policies
* Environment checks

---

## 🔥 Scenario 7: Rollback Strategy

**Q:**
Production deployment failed. How do you rollback?

**Expected discussion:**

* Artifact versioning
* Redeploy previous build
* Infrastructure rollback

---

## 🔥 Scenario 8: Infrastructure & App Pipelines

**Q:**
Do you separate infra and application pipelines? Why?

**Expected answer:

* Separation of concerns
* Independent lifecycle
* Risk reduction

---

## 🔥 Scenario 9: Pipeline Security Breach

**Q:**
Someone modified pipeline YAML to expose secrets. What do you do?

**Expected actions:**

* Branch protection
* Required reviewers
* Rotate secrets
* Audit logs

---

## 🔥 Scenario 10: Cross-Project Pipelines

**Q:**
How do you consume artifacts from another Azure DevOps project?

**Expected answer:**

* Pipeline resources
* Permissions
* Service connections

---

# 🔹 Advanced Azure DevOps Questions (4+ Years Level)

46. What is pipeline caching?
47. How do you template YAML pipelines?
48. What are reusable YAML templates?
49. How do you implement GitOps with Azure DevOps?
50. What is Azure DevOps REST API?
51. How do you manage large YAML pipelines?
52. How do you integrate Azure DevOps with AKS?
53. How do you handle secrets with Azure Key Vault?
54. How do you monitor pipeline health?
55. Azure DevOps vs GitHub Actions – when to choose what?

---

# ✅ Interview Tip (Very Important)

For your experience level:

* Always answer with **real pipeline designs**
* Mention **security, approvals, and rollback**
* Explain **why Azure DevOps was chosen**
* Talk about **scalability and governance**

Example answer:

> “We used multi-stage YAML pipelines with Azure Key Vault, Terraform backend in Azure Storage, environment approvals for prod, and self-hosted agents for private VNets…”

---

If you want next:

* 🔹 **Azure DevOps YAML examples**
* 🔹 **End-to-end CI/CD pipeline (Terraform + Docker + AKS)**
* 🔹 **Mock Azure DevOps interview**
* 🔹 **Azure DevOps vs Jenkins comparison table**

Just tell me 👍
