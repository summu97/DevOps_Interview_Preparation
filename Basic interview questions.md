### What are your day to day activities as a **DevOps Engineer**
* I work closely with development teams to support daily build and deployment activities using Jenkins CI/CD pipelines. My responsibilities include managing and updating infrastructure using Terraform, implementing and maintaining Kubernetes deployments, and making necessary changes to Jenkins pipelines based on project requirements. I regularly troubleshoot and resolve issues related to Kubernetes deployments, pod health, and CI/CD pipeline failures. Additionally, I monitor application and cluster status, support new feature requirements, and ensure reliable and smooth delivery across environments.
---
### Tell me about yourself
**Answer:**

> My name is **Sumanth Bade**, and I’m from **Kondapur, Hyderabad**. I have around **2 years of experience as a DevOps Engineer**, and I’m currently working at **Adaequare Info Pvt. Ltd.**, where I joined in **February 2024**.
>
> In my current project, I support a **microservices-based music application with around 13 services**. I work extensively with **Terraform for infrastructure provisioning**, **Jenkins for CI/CD automation**, and **Kubernetes for application deployments and operations**.
>
> My day-to-day responsibilities include managing infrastructure changes, maintaining and enhancing Jenkins pipelines, supporting **daily builds**, troubleshooting **Kubernetes deployment issues, pod failures, and CI/CD pipeline errors**, and working closely with developers to implement new requirements. I’m also involved in monitoring application health and ensuring stable and reliable deployments across environments.
>
---

### Why do you want to change to new company
* As our current project is nearing production go-live, most of the core DevOps work—CI/CD pipelines, infrastructure provisioning, and automation—has already been completed. At this stage, the role has become largely maintenance-focused rather than implementation-driven. I’m now looking for a new opportunity where I can explore and work with more DevOps tools, and showcase my skills by automating the entire SDLC end to end.

---

### What are DevOps best practices for Infrastructure as Code (IaC)

* Store all IaC code in **version control (Git)**
* Use **remote state with locking**
* Separate **dev/test/prod environments**
* Write **modular and reusable code**
* Use **variables**, avoid hardcoding
* Follow **least-privilege IAM**
* Always run **plan before apply**
* Automate via **CI/CD pipelines**
* Never store **secrets in code**
* Apply **naming and tagging standards**
* Detect **configuration drift**
* Keep infrastructure **idempotent**
* Document IaC clearly

---

### What are DevOps best practice for CI/CD

* **Store all code (app, IaC, pipelines) in version control** → tracks changes & enables collaboration
* **Use multiple pipeline stages (build, test, scan, deploy)** → keeps pipelines organized & modular
* **Require approvals for deployments** → adds safety for production changes
* **Run Sonar and Trivy for code & vulnerability scanning** → ensures code quality & security
* **Execute test cases in a separate stage** → verifies functionality before deployment
* **Fetch secrets from Key Vaults / Secret Managers** → avoids hardcoding sensitive data
* **Keep Jenkinsfile in version control** → versioned pipeline as code
* **Maintain declarative pipelines for build automation** → ensures reproducibility & easier maintenance


