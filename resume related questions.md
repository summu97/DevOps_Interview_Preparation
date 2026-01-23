### Scan a Docker image for vulnerabilities?
```bash
trivy image nginx:latest
trivy image --severity HIGH,CRITICAL nginx:latest
```
---

### What are StatefulSets?
* StatefulSets are Kubernetes objects used for applications that need a stable identity, such as fixed pod names, stable network identities, and dedicated persistent storage (one PV/PVC per pod).
They are commonly used for databases, Kafka, and other stateful applications.
* StatefulSets are used for stateful applications that need stable pod identity and persistent storage, like databases and Kafka.

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


### DevOps best practices for Infrastructure as Code (IaC)

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

### DevOps best practice for CI/CD

* **Store all code (app, IaC, pipelines) in version control** → tracks changes & enables collaboration
* **Use multiple pipeline stages (build, test, scan, deploy)** → keeps pipelines organized & modular
* **Require approvals for deployments** → adds safety for production changes
* **Run Sonar and Trivy for code & vulnerability scanning** → ensures code quality & security
* **Execute test cases in a separate stage** → verifies functionality before deployment
* **Fetch secrets from Key Vaults / Secret Managers** → avoids hardcoding sensitive data
* **Keep Jenkinsfile in version control** → versioned pipeline as code
* **Maintain declarative pipelines for build automation** → ensures reproducibility & easier maintenance

---

### Why do you want to change to new company
* As our current project is nearing production go-live, most of the core DevOps work—CI/CD pipelines, infrastructure provisioning, and automation—has already been completed. At this stage, the role has become largely maintenance-focused rather than implementation-driven. I’m now looking for a new opportunity where I can explore and work with more DevOps tools, and showcase my skills by automating the entire SDLC end to end.


