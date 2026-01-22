# 🔹 Docker – Theoretical Interview Questions

### What is **round robin** in docker?
* **Round Robin in Docker** is a **load balancing strategy** where requests are **distributed equally and sequentially** across all running container instances.
* Round Robin is a strategy to distribute traffic evenly among available service replicas.

**Simpler version:**
> Round Robin = send requests to containers one after another in a loop.

**Example:**
* 3 containers (`A`, `B`, `C`)
* Requests go: 1 → A, 2 → B, 3 → C, 4 → A, 5 → B…

## 1️⃣ Docker Fundamentals

### 1. What is Docker and why is it used?

---

### 2. Difference between:
  * Virtual Machines vs Containers

---

### 3. What problem does Docker solve in DevOps?
- Before Docker:
  * App works on developer’s laptop
  * Fails in QA / staging / production
  * Different OS, libraries, versions, configs
- Docker:
  * Packages application + dependencies + runtime
  * Runs the same way everywhere

---

### 4. What is **containerization**?

---

### 5. Explain Docker architecture.

---

### 6. What is Docker Engine?
- Docker Engine is the software that allows you to create and run containers on a machine.

---

### 7. Explain:
   * Docker Client
   * Docker Daemon
   * Docker Registry

---

### 8. What is Docker Desktop vs Docker Engine?
- Docker Desktop
  * UI-based tool (GUI included)
  * Mainly for developers

Runs on:
  * Windows
  * macOS
  * Linux
  * Uses a VM internally to run Docker Engine

- Docker Engine:
  * Core Docker runtime
  * Native on Linux (no VM needed)
  * CLI-based, no GUI
  * Used on servers / production / CI/CD

---

### 9. What is the role of `containerd`?
- containerd is responsible for pulling images, creating containers, starting/stopping them, and managing their lifecycle.

Docker CLI / Kubernetes
        ↓
     Docker Engine
        ↓
     containerd
        ↓
       runc
        ↓
     Linux Kernel

---

### 10. How Docker uses Linux kernel features?
- Docker uses Linux kernel features like namespaces for isolation, cgroups for resource limits, and union filesystems for layered images to run containers efficiently without virtualizing hardware.

---

### 11. What is a Docker image?

---

### 12. What is a Docker container?

---

### 13. Difference between:

* Image vs Container

---

### 14. What happens when you run `docker run`?

---

### 15. What is an image layer?
- An image layer is just the component that we specify inside Dockerfile structured or formed one after other

---

### 16. Why are Docker images immutable?
- Docker images are immutable to ensure consistent, repeatable, and predictable environments; any change creates a new layer rather than modifying the original image.
- Docker images are immutable because once built, they cannot be changed.

---

### 17. What is a Dockerfile?

---

### 18. Common Dockerfile instructions:

* `FROM`
* `RUN`
* `CMD`
* `ENTRYPOINT`
* `COPY` vs `ADD`

### 19. Difference between `CMD` and `ENTRYPOINT`?
- CMD
  * Sets **default command or arguments** for the container
  * Can be **easily overridden** when running the container

**Example:**

```dockerfile
CMD ["echo", "Hello"]
```

```bash
docker run myimage        # prints Hello
docker run myimage Bye    # prints Bye
```

---

- ENTRYPOINT
  * Sets the **main fixed executable** for the container
  * Harder to override (only with `--entrypoint`)
  * CMD can provide **default arguments** to it

**Example:**

```dockerfile
ENTRYPOINT ["echo"]
CMD ["Hello"]
```

```bash
docker run myimage        # prints Hello
docker run myimage Bye    # prints Bye
```

---

### Quick rule

> **ENTRYPOINT = what runs, CMD = default arguments**

---

### 20. What is **multi-stage build**?

---

### 21. Why should we avoid running containers as root?
- Running containers as root is unsafe because it can allow attackers to gain host-level access; containers should run as a non-root user following the principle of least privilege.

How to avoid root

Use Dockerfile instruction:
```dockerfile
USER appuser
```

Create a non-root user in Dockerfile:
```dockerfile
RUN useradd -m appuser
USER appuser
```

---

### 22. What is `.dockerignore` and why is it important?
- .dockerignore is a file that tells Docker which files and directories to ignore when building an image.
- Example .dockerignore
```bash
node_modules
*.log
.git
.env
```
---

### 23. Docker network types:

* Bridge
* Host
* None
* Overlay

---

### 24. Difference between:

* Bridge vs Host network

---

## 1️⃣ Bridge Network

* **Default network type** for Docker containers
* Each container gets its **own private IP** inside a virtual network
* **Communication:**

  * Containers on the **same bridge** can talk to each other via container IP or name
  * To communicate with the host or outside, Docker **NATs traffic** through host IP
* **Use case:** Multiple containers on the same host needing isolation

**Example:**

```bash
docker network create mybridge
docker run --network mybridge nginx
```

---

## 2️⃣ Host Network

* Container **shares the host’s network stack**
* No separate container IP
* **Communication:**

  * Container uses **host IP** directly
  * Ports used by container are **directly the host ports**
* **Use case:** High-performance networking, low-latency, no NAT overhead

**Example:**

```bash
docker run --network host nginx
```
## Overlay network for container communication in clusters
##  None network is a special Docker network that gives a container no network connectivity at all.


---

### Interview-friendly summary

> **Bridge network isolates containers with their own IPs and NAT for outside communication, while host network shares the host’s network stack, giving direct access to host IP and ports.**
---
# How containers communicate with each other in bridge network?
* Bridge network allows containers on the same host to communicate with each other using their own private IP addresses assigned by Docker.
* Communication is within the same host
* Each container gets its own IP
* Traffic does not leave the host
* Containers on different hosts cannot communicate via bridge (needs overlay)
```bash
docker network create app-net
docker run --name web --network app-net nginx
docker run --name api --network app-net busybox
```

---

### How containers communicate with each other in host network?
* In host networking, containers share the host’s network stack and communicate via localhost or host IP, just like normal processes, without Docker bridge or NAT.
```bash
docker run --network host nginx

# Container A listens on port 8080
curl http://localhost:8080

```

---

### When Host Network Is Actually Used?
* Monitoring agents (Prometheus node-exporter), Log collectors

---

### How containers communicate with each other in none network?
* The none network is used for maximum isolation, running batch or secure workloads that do not require any network access.

---

### How containers communicate with each other in ovrlay network?
* In an overlay network, each container (or pod) gets its own private IP address.
* Key Ports Used:
| Purpose                    | Port           |
| -------------------------- | -------------- |
| VXLAN encapsulation        | UDP `4789`     |
| Cluster management (Swarm) | TCP `2377`     |
| Node discovery             | TCP/UDP `7946` |

---

* Bridge → apps (single host)
* Host → infra agents / monitoring
* None → security, batch, CI, isolation
* Overlay → microservices, multi-host clusters

---

### I have to run 4 containers on the same port what is the best approach?
* Yes, on a single host I can run multiple instances of the same container on different ports and use NGINX as a reverse proxy to load-balance traffic across them.

---

### 25. How do containers communicate with each other?
- Containers communicate with each other using Docker networking.
1️⃣ On the same host
- Bridge network (default):
* Containers get private IPs on the bridge network
- Can communicate using:
* Container name (Docker DNS)
* IP address

- Host network
* Containers share the host network stack
* Can communicate via host IP and ports
* No NAT needed

2️⃣ Across multiple hosts (Cluster)
- Overlay network
* Creates a virtual network across hosts
* Containers on different hosts can communicate as if on the same LAN
- Used in:
* Docker Swarm
* Kubernetes (via CNI plugins)

---

### 26. How does Docker DNS work?

**Docker automatically handles DNS inside networks:**

* **User-defined networks (bridge/overlay):**

  * Each container’s hostname = container name
  * Docker runs an **internal DNS server**
  * Containers can resolve other container names to IPs automatically

* **Default bridge network:**

  * DNS works **only with linked containers** (`--link`)
  * Using container names directly doesn’t work

* **Overlay networks / Swarm (multi-host):**

  * Docker DNS is **cluster-aware**
  * Resolves **service names to all container replicas**
  * Enables **load balancing** between containers

---

### 27. What happens when you expose a port?


## 1️⃣ `EXPOSE` in Dockerfile

```dockerfile
FROM nginx
EXPOSE 80
```

* Tells Docker that the container **will listen on port 80**
* **Documentation only**: it does **not publish the port to the host**
* Helps tools like Docker Compose or orchestration know which ports the container uses

---

## 2️⃣ Exposing vs publishing

| Action                   | What happens                                                    |
| ------------------------ | --------------------------------------------------------------- |
| `EXPOSE 80` (Dockerfile) | Container declares it will listen on port 80                    |
| `docker run -p 8080:80`  | Maps container port 80 → host port 8080 (accessible externally) |

> **Important:** EXPOSE alone ≠ access from host

---

## 3️⃣ How it works with Docker networks

* Inside a network, **other containers can access the exposed port** by container name and port
* External access still requires `-p` or `--publish`

---

## 4️⃣ Interview-ready one-liner

> **`EXPOSE` declares a port the container will listen on; it does not publish it to the host—publishing is done with `-p` or `--publish`.**

---

### 28. Difference between:

* Volume
* Bind mount
* tmpfs

---

## 1️⃣ Volume

* Docker-managed storage
* Created via `docker volume create` or automatically
* Stored under Docker’s folder (usually `/var/lib/docker/volumes/`)
* **Data persists** even if the container is removed
* Can be **shared between multiple containers**

**Example:**

```bash
docker volume create my_data
docker run -v my_data:/app/data nginx
```

---

## 2️⃣ Bind mount

* Maps a **specific host directory** into a container
* Example: `/host/path:/container/path`
* Data persists **on the host**, independent of container
* **Changes in container reflect on host and vice versa**

**Example:**

```bash
docker run -v /home/user/app:/app nginx
```

> Note: Data does **not disappear** when container is removed; it stays on the host.

---

## 3️⃣ tmpfs mount

* **In-memory storage** (RAM), not on disk
* Lives **only during container lifetime**
* Fast, temporary, automatically cleared when container stops
* Useful for **sensitive data or caching**

**Example:**

```bash
docker run --tmpfs /app/tmp nginx
```

* `/app/tmp` exists in memory
* Disappears when container stops

---

### 29. When would you use Docker volumes?
- Use Docker volumes to persist data beyond container lifetimes, share data between containers, and keep storage managed by Docker instead of the host.

---

### 30. Where are Docker volumes stored?
- /var/lib/docker/volumes/

---

### 31. What happens to container data after container deletion?
- Container data in the writable layer or tmpfs is lost on deletion, while data in volumes or bind mounts persists independently of the container.

---

### 32. How do you limit CPU and memory for a container?
- You can limit **CPU and memory** for a Docker container using **resource constraints** with the `docker run` command.

---

## 1️⃣ Memory Limit

* Use `--memory` (or `-m`) to set the **maximum memory** the container can use
* Use `--memory-swap` to limit **swap + memory**

**Example:**

```bash
docker run -it -m 512m --memory-swap 1g ubuntu
```

* `-m 512m` → max 512 MB RAM
* `--memory-swap 1g` → max 1 GB including swap

---

## 2️⃣ CPU Limit

* Use `--cpus` to restrict CPU usage
* Example:

```bash
docker run -it --cpus="1.5" ubuntu
```

* Container can use at most **1.5 CPU cores**

### Other CPU options:

| Option          | Description                         |
| --------------- | ----------------------------------- |
| `--cpu-shares`  | Relative weight (default 1024)      |
| `--cpuset-cpus` | Pin container to specific CPU cores |

**Example:**

```bash
docker run -it --cpuset-cpus="0,1" ubuntu
```

* Container only runs on CPU cores 0 and 1

---

## 3️⃣ Combine CPU & Memory

```bash
docker run -it -m 512m --cpus="1" ubuntu
```

* Limits container to **512 MB RAM** and **1 CPU**

---

### Interview-ready one-liner

> **Docker allows resource limits using `--memory` for RAM and `--cpus` (or `--cpu-shares`/`--cpuset-cpus`) for CPU to control container resource usage.**

---

### 33. What happens when a container exceeds memory limits?
- If a container exceeds its memory limit, Docker (via the kernel OOM killer) kills the container to prevent it from using more than the allocated memory.

---

### 34. What is Docker namespace and cgroups?
## 1️⃣ Namespaces → Isolation

* **What it does:** Makes each container feel like it has its **own separate system**
* **Isolates:** Processes, network, filesystem, hostname, users, IPC
* **Example:** Container processes cannot see host or other container processes

**Analogy:** Each container lives in its **own sandbox**

---

## 2️⃣ cgroups → Resource Limits

* **What it does:** Controls how much **CPU, memory, disk, network** a container can use
* **Example:**

```bash
docker run -it -m 512m --cpus="1" ubuntu
```

* Limits container to 512 MB RAM and 1 CPU

**Analogy:** Each container has its **own quota** of system resources

---

### One-line summary

> **Namespaces isolate containers from each other and the host, while cgroups control how much resources they can use.**

---

### 35. How do you secure Docker containers?

1. **Run as non-root**

   * Use `USER` in Dockerfile to avoid root

2. **Limit resources**

   * Restrict CPU, memory, and I/O with `--cpus` and `-m`

3. **Use trusted images**

   * Only official or verified images; scan for vulnerabilities

4. **Read-only filesystem**

   * Prevent unwanted writes using `--read-only`

5. **Limit capabilities**

   * Drop unnecessary Linux capabilities (`--cap-drop`)

6. **Manage secrets safely**

   * Use Docker secrets instead of hardcoding passwords

7. **Secure networking**

   * Use user-defined networks, avoid exposing unnecessary ports

8. **Keep everything updated**

   * Update Docker Engine, host OS, and images regularly

---

### One-liner

> **Secure containers by running non-root, limiting resources, using trusted images, restricting capabilities, managing secrets, and controlling network access.**


36. What is Docker Content Trust?


* **Docker Content Trust (DCT)** ensures that **images you pull or push are signed and verified**.
* It uses **digital signatures** to confirm the authenticity and integrity of Docker images.
* Helps prevent running **untrusted or tampered images**.

---

## Key points

1️⃣ **Enabled via environment variable**

```bash
export DOCKER_CONTENT_TRUST=1
```

2️⃣ **How it works**

* When enabled:

  * `docker pull` verifies the image signature before downloading
  * `docker push` signs the image automatically (if you have signing keys)

3️⃣ **Benefits**

* Prevents **tampered or fake images** from being used
* Ensures **provenance** — you know the image comes from a trusted source

4️⃣ **When it’s disabled**

* Docker pulls images normally without verifying signatures

---

### Interview-ready one-liner

> **Docker Content Trust ensures Docker images are signed and verified, protecting against untrusted or tampered images.**

---

## 8️⃣ Docker Compose

### 37. What is Docker Compose?

---

### 38. Difference between:

* `docker-compose` vs `docker run`

---

### 39. What is `depends_on`?

* **`depends_on`** is used in **Docker Compose** to specify that a service **depends on another service**.
* Ensures **startup order**: Docker starts dependent services **before** the service that depends on them.

---

## Example

```yaml
version: '3'
services:
  db:
    image: mysql
  web:
    image: my-web-app
    depends_on:
      - db
```

* Here, `web` depends on `db`
* Docker Compose **starts `db` first**, then `web`

---

## Important Notes

1. **Startup order only** – `depends_on` does **not wait for the service to be “ready”** (like MySQL accepting connections).

   * For readiness, use **healthchecks**.
2. Works in **Docker Compose files**, not plain Docker run commands.

---

### Interview-ready one-liner

> **`depends_on` in Docker Compose specifies service startup order, ensuring dependent services start before the service that relies on them.**

---

### 40. Can Docker Compose be used in production?


### ✅ Yes, but mostly for:

* Small to medium apps
* Single-host setups

### ⚠️ Limitations:

* Not for multi-host clusters
* No auto-scaling or advanced load balancing

### 🔹 Tips for production:

* Use restart policies (`restart: always`)
* Use volumes for persistent data
* Manage secrets & environment variables
* Monitor container health

---

### One-liner

> **Docker Compose can run production apps on a single host, but for large-scale or multi-host deployments, tools like Docker Swarm or Kubernetes are better.**

---

## 9️⃣ Image Registry & Distribution

### 41. What is Docker Hub?

---

### 42. Difference between:

* Public registry
* Private registry

---

### 43. How do you push images to a private registry?

---

### 44. How do you tag Docker images properly?
- <registry>/<repository>/<image>:<tag>

---

## 10️⃣ Docker Best Practices

### 45. How do you reduce Docker image size?
- multi stage builds, or distroless images
- Reduce Docker image size using multi-stage builds, minimal base or distroless images, .dockerignore, and by cleaning up unnecessary files and layers.

---

### 46. Why use Alpine images?


* **Alpine images are very small (~5 MB)** compared to standard images like Ubuntu or Debian (100+ MB)
* They provide a **minimal Linux environment** with just the essentials
* You can **add only the packages your app needs**, keeping the image lightweight and efficient

---

### Interview-friendly summary

> **Alpine images are used to create smaller, faster, and more secure Docker containers.**

---

### 47. Why should we minimize layers?
- Minimizing layers reduces image size, speeds up builds, simplifies maintenance, and improves security.

---

### 48. How do you scan Docker images for vulnerabilities?
- Using Docker CLI (docker scan): docker scan myimage:tag
- Using third-party tools: trivy image myimage:tag

---

# 🔹 Scenario-Based Docker Interview Questions (VERY IMPORTANT)

---

## 🔥 Scenario 1: Container Crashing

**Q:**
Your container keeps restarting after deployment. How do you troubleshoot it?

**Expected areas:**

* `docker logs`
* `docker inspect`
* Exit codes
* Health checks
* ENTRYPOINT/CMD issues

---

## 🔥 Scenario 2: Large Image Size

**Q:**
Your Docker image is 1.5GB. How will you reduce it?

**Expected solutions:**

* Multi-stage builds
* Smaller base image
* `.dockerignore`
* Remove build dependencies
* Layer optimization

---

## 🔥 Scenario 3: Data Persistence

**Q:**
A container restarts and application data is lost. Why? How do you fix it?

**Expected points:**

* Volumes
* Bind mounts
* Stateful vs stateless containers

---

## 🔥 Scenario 4: Port Not Accessible

**Q:**
Container is running but application is not accessible from browser.

**Expected checks:**

* Port mapping
* App listening address (`0.0.0.0`)
* Firewall rules
* Docker network

---

## 🔥 Scenario 5: Inter-Container Communication

**Q:**
Two containers need to talk to each other securely. How do you design it?

**Expected answer:**

* User-defined bridge network
* Container DNS names
* Avoid hard-coded IPs

---

## 🔥 Scenario 6: Secrets in Docker

**Q:**
How do you pass secrets (DB passwords, API keys) to Docker containers?

**Expected solutions:**

* Environment variables
* Docker secrets (Swarm)
* External secret managers
* Avoid hardcoding in images

---

## 🔥 Scenario 7: Zero-Downtime Update

**Q:**
How do you update a running container without downtime?

**Expected discussion:**

* Blue-Green deployment
* Rolling updates
* Load balancer
* Docker Swarm / Kubernetes

Here’s a **clear explanation** of how to update a running container **without downtime**:

---

## 1️⃣ Concept: **Zero-downtime deployment**

* Goal: Replace an old container with a new version **without interrupting users**
* Achieved using strategies like **in-place updates, blue/green, or rolling updates**

---

## 2️⃣ Methods

### **A. Blue/Green Deployment**

* **Blue** = current version
* **Green** = new version
* Steps:

  1. Deploy new container (Green) alongside old (Blue)
  2. Test Green container
  3. Switch traffic from Blue → Green (via load balancer)
  4. Remove Blue container

**Example:** Docker + Nginx load balancer

```text
User → Nginx → Blue container (old)
Deploy Green container
Switch Nginx routing → Green
Remove Blue
```

---

### **B. Rolling Updates**

* Gradually replace old containers with new ones
* Usually done with **Docker Swarm or Kubernetes**
* Ensures **at least one instance is always serving traffic**

**Example in Swarm:**

```bash
docker service update --image myapp:v2 my_service
```

---

### **C. In-place Update (less recommended)**

* Stop old container → start new container quickly
* Might cause **brief downtime**, so better with load balancer

---

## 3️⃣ Tips

* Always use **stateless containers** if possible
* Use **persistent volumes** for data
* Use **health checks** to ensure new container is ready before switching traffic

---

### Interview-ready one-liner

> **To update a running container without downtime, use blue/green deployments, rolling updates in Swarm/Kubernetes, or in-place updates with load balancers and health checks.**

---



---

## 🔥 Scenario 8: High CPU Usage

**Q:**
A container is consuming 100% CPU. How do you control and debug it?

**Expected areas:**

* CPU limits
* `docker stats`
* Application profiling

---

## 🔥 Scenario 9: Image Vulnerabilities

**Q:**
A vulnerability is reported in your Docker image. What do you do?

**Expected steps:**

* Rebuild image
* Patch base image
* Scan images
* Redeploy

---

## 🔥 Scenario 10: Docker in CI/CD

**Q:**
How do you integrate Docker into CI/CD pipelines?

**Expected approach:**

* Build image
* Scan image
* Push to registry
* Deploy

---

# 🔹 Advanced Docker Questions (4+ Years Level)

### 51. Difference between Docker Swarm and Kubernetes?

---

### 52. What happens if Docker daemon crashes?
- dockerd is the background service that manages containers, images, networks, and volumes
- If the Docker daemon crashes, running containers stop, data in volumes persists, and containers with restart policies can automatically restart when the daemon comes back.

---

### 53. Explain rootless Docker.
- Rootless Docker runs the Docker daemon and containers as a non-root user, improving security by reducing host privileges and risks.

---

### 54. How does Docker handle logging?
Exactly ✅

* Docker captures both **stdout (normal output)** and **stderr (errors)** of containers.
* By default, these logs are stored in the host at:

```
/var/lib/docker/containers/<container-id>/<container-id>-json.log
```

* You can view them using:

```bash
docker logs <container_name_or_id>
```

* For production, you can also **send logs to syslog, Fluentd, CloudWatch, or other centralized systems** using logging drivers.

---

### One-liner

> **Docker captures container standard output and error streams and logs them in JSON files by default, but logging drivers can redirect them elsewhere.**

---

### 55. What are distroless images?

---

### 56. How do you debug containers in production?
- Debug containers in production using logs (docker logs), inspecting containers, exec/attach commands, monitoring resources, and centralized logging or monitoring tools.

---

### 57. Explain OCI (Open Container Initiative).
Exactly ✅

* **OCI is a standard** for:

  1. **Container images** – how images are built and stored
  2. **Container runtimes** – how containers are executed

* **Purpose:** Makes containers **portable and flexible**, so any OCI-compliant runtime (Docker, Podman, runc, Kubernetes, etc.) can run the images consistently.

---

### One-liner

> **OCI ensures container images and runtimes follow a standard, allowing containers to run anywhere reliably.**

---


