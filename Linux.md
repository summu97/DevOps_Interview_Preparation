---

# 🔥 LINUX + DEVOPS SCENARIO-BASED INTERVIEW QUESTIONS (4+ YEARS)

---

## 1️⃣ **Process, CPU, Memory & Performance Scenarios**

### Q1.

A production server is **slow**, load average is very high, but CPU usage is low.
**What could be the reason and how do you troubleshoot?**

---

### Q2.

A Java application suddenly crashes with:

```
java.lang.OutOfMemoryError: unable to create new native thread
```

**What Linux limits could cause this? How do you fix it?**

---

### Q3.

One process is consuming **100% CPU on a multi-core system**.
How do you:

* Identify the exact thread?
* Trace what it is doing?

---

### Q4.

A server has **free memory**, but applications are still getting killed.
Logs show:

```
Out of memory: Kill process xxxx
```

Why does this happen?

---

### Q5.

How do you differentiate between:

* CPU bottleneck
* Memory bottleneck
* Disk I/O bottleneck
  using Linux tools?

---

## 2️⃣ **ulimit & System Limits (VERY IMPORTANT)**

### Q6.

A Dockerized application fails with:

```
Too many open files
```

What does this mean at the Linux level?
Which limits do you check and modify?

---

### Q7.

What is the difference between:

```
ulimit -n
ulimit -u
ulimit -f
```

When would you change each?

---

### Q8.

You increased `ulimit -n` but after reboot, the value resets.
**How do you make it permanent?**

---

### Q9.

Your application needs **50,000 concurrent connections**.
Which Linux parameters must be tuned besides `ulimit`?

---

### Q10.

How do **systemd service limits** override `ulimit` settings?

---

## 3️⃣ **Docker + Linux Internals**

### Q11.

A container crashes but works fine on a VM.
What Linux-level differences could cause this?

---

### Q12.

How does Docker use:

* Namespaces
* cgroups
  Explain with real examples.

---

### Q13.

A container is OOM-killed even though the host has free memory.
Why?

---

### Q14.

How do you limit:

* CPU
* Memory
* File descriptors
  for a Docker container?

---

### Q15.

How do you debug **network issues inside a container** when tools like `ping` are missing?

---

## 4️⃣ **Disk, Filesystem & Storage Scenarios**

### Q16.

Disk shows **100% usage**, but `du -sh /*` does not show large files.
What could be the reason?

---

### Q17.

A deleted log file is still consuming disk space.
Why and how do you free the space?

---

### Q18.

Difference between:

* Inode exhaustion
* Disk space exhaustion
  How do you detect both?

---

### Q19.

How do you troubleshoot **slow disk I/O** in Linux?

---

### Q20.

What happens if `/tmp` is full?
Which applications can break?

---

## 5️⃣ **Networking Scenarios**

### Q21.

A service is running but not accessible from another server.
List your Linux-level troubleshooting steps.

---

### Q22.

Difference between:

* `netstat`
* `ss`
* `lsof -i`

---

### Q23.

How do you identify **which process is listening on a port**?

---

### Q24.

High number of connections in `TIME_WAIT`.
Why does this happen and how do you reduce it?

---

### Q25.

How does Linux handle **DNS resolution** and how do you debug DNS issues?

---

## 6️⃣ **System Startup, Services & systemd**

### Q26.

A service works when started manually but fails on reboot.
What could be wrong?

---

### Q27.

Difference between:

* `service`
* `systemctl`
* SysV vs systemd

---

### Q28.

How do you analyze **why a service failed to start**?

---

### Q29.

How do you run a service with a **specific user, ulimit, and environment variables**?

---

## 7️⃣ **Security & Permissions**

### Q30.

An application cannot bind to port 80 unless run as root.
Why? How do you fix it **without running as root**?

---

### Q31.

Difference between:

* File permissions
* ACLs
* SELinux/AppArmor

---

### Q32.

An app works in one server but fails in another due to permission denied.
How do you debug?

---

## 8️⃣ **Logs, Monitoring & Debugging**

### Q33.

Where would you check logs for:

* Kernel issues
* systemd services
* Docker containers

---

### Q34.

How do you troubleshoot a **hung process**?

---

### Q35.

What is the difference between:

* `strace`
* `ltrace`
  When would you use them?

---

## 9️⃣ **Production Incident Scenarios (Real DevOps Level)**

### Q36.

At midnight, your application becomes unresponsive:

* CPU is normal
* Memory is normal
* Disk is normal
  What do you check next?

---

### Q37.

A cron job runs manually but not automatically.
What could be the reasons?

---

### Q38.

How do you safely restart a critical service without downtime?

---

### Q39.

A Linux server reboots unexpectedly.
How do you investigate the cause?

---

### Q40.

How do you design Linux servers for **high availability and fault tolerance**?

---

## 10️⃣ **Quick Fire (Interview Favorites)**

* Difference between **load average** and CPU usage?
* What is **OOM killer**?
* What is **nice & renice**?
* What is **swap** and when should it be used?
* Difference between **hard and soft limits**?
* Difference between **zombie and orphan processes**?
* What happens when you run `kill -9`?

---



====================================


---

# 🔥 DEVOPS-ONLY LINUX INTERVIEW QUESTIONS

**(Processes • Metrics • Debugging • Production Scenarios)**

---

## 🔍 1. Processes & Services (Very Common)

### Q1.

An application is running but users say it’s slow.
Which **process-level metrics** do you check first and why?

---

### Q2.

How do you identify:

* Which processes are running?
* Which user started them?
* How long they have been running?

---

### Q3.

How do you find the **parent process** of a running process?
Why is this useful in debugging?

---

### Q4.

A process keeps restarting automatically.
How do you find **who is restarting it**?

---

### Q5.

How do you detect **zombie processes** and what do they indicate?

---

### Q6.

Difference between:

* `ps`
* `top`
* `htop`
* `atop`
  When do you use each in production?

---

## 📊 2. Metrics & Monitoring (REAL DevOps Questions)

### Q7.

Which Linux metrics would you monitor to detect **performance degradation early**?

---

### Q8.

CPU usage is normal but application latency is high.
Which Linux metrics do you check next?

---

### Q9.

What is **load average** and why is it more important than CPU %?

---

### Q10.

How do you identify **disk I/O bottlenecks** at OS level?

---

### Q11.

Which memory metrics matter more for applications:

* free
* available
* cache
* swap
  Why?

---

### Q12.

How do you correlate **Linux metrics** with **application metrics**?

---

## 🐞 3. Debugging & Troubleshooting (INTERVIEW GOLD)

### Q13.

A service is running, port is open, but API requests time out.
What Linux checks do you perform?

---

### Q14.

How do you debug a **hung process** without restarting it?

---

### Q15.

What tools do you use to debug:

* High CPU
* High memory
* High I/O
* Network slowness

---

### Q16.

Application logs show nothing, but users report failures.
What Linux-level logs do you inspect?

---

### Q17.

How do you trace **system calls** made by a process?
Why would you do this?

---

### Q18.

Difference between:

* `kill`
* `kill -9`
* graceful shutdown
  Why does DevOps care?

---

## 🐳 4. Containers & DevOps Debugging

### Q19.

A container is running but application inside is not responding.
What Linux/container-level checks do you do?

---

### Q20.

Container restarts repeatedly with no logs.
Where do you debug?

---

### Q21.

How do you inspect **resource usage per container** from host?

---

### Q22.

Why does an application work on VM but fail inside Docker?

---

### Q23.

How do Linux limits (`ulimit`) affect containers?

---

### Q24.

How do you debug networking issues inside a container?

---

## 🚨 5. Production Incident Scenarios

### Q25.

CPU spikes suddenly during peak traffic.
How do you identify **which process caused it**?

---

### Q26.

A critical service is down after deployment.
How do you quickly verify whether it’s:

* App issue
* OS issue
* Resource issue

---

### Q27.

Server is reachable, but deployment pipelines fail.
Which Linux checks do you perform?

---

### Q28.

A background job is running slower over time.
How do you debug it?

---

### Q29.

Application stops accepting connections after some time.
What Linux limits could cause this?

---

### Q30.

You see frequent OOM kills in logs.
How do you identify the root cause?

---

## 📈 6. CI/CD & Automation Impact (DevOps-Specific)

### Q31.

A script works manually but fails in Jenkins.
Which Linux differences do you check?

---

### Q32.

Why do cron jobs behave differently from shell execution?

---

### Q33.

How do you debug permission issues in CI runners?

---

### Q34.

How do you ensure **Linux tuning consistency** across environments?

---

### Q35.

How do you detect **resource leaks** in long-running CI jobs?

---

## 🧠 7. Quick Senior-Level Checks

* How do you identify which process is using a file?
* How do you check open sockets per process?
* How do you know if a process is CPU-bound or I/O-bound?
* How do you confirm a process is stuck on disk or network?
* How do you debug a service without stopping it?

---
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Got it 👍
Below is your **cleaned-up, DevOps-focused Linux interview question list**, keeping **your exact structure and style**, but **removing generic/theoretical questions** and keeping **only what a 4+ years DevOps engineer is expected to know** — **processes, metrics, debugging, services, containers, limits, networking, logs, production scenarios**.

---

# 🔹 Linux – DevOps Interview Questions (4+ Years)

---

## 1️⃣ Linux Fundamentals (DevOps-Oriented)

1. Which Linux distributions have you used in production and why?
2. How do you check OS version, kernel version, and architecture?
3. Difference between user space and kernel space.
4. What happens when a Linux command is executed internally?

---

## 2️⃣ Boot & System Initialization (Ops Focus)

5. Explain Linux boot process at a high level.
6. What is GRUB and when do you troubleshoot it?
7. What is `systemd` and why is it important for DevOps?
8. Difference between:

   * `systemctl` vs `service`
9. How do you debug a server stuck at boot?

---

## 3️⃣ Filesystem & Storage (Production Scenarios)

10. Important directories for DevOps troubleshooting:

* `/var`
* `/etc`
* `/proc`
* `/sys`
* `/tmp`

11. Difference between `ext4` and `xfs` (real-world usage).
12. What is an inode and how does inode exhaustion affect systems?
13. Hard link vs Soft link (practical use cases).
14. What happens when disk is 100% full?
15. How do you find which directory is consuming most space?

---

## 4️⃣ Permissions & Security (Real Ops Issues)

16. How do Linux file permissions work?
17. Difference between:

* `chmod`
* `chown`

18. What are SUID, SGID, Sticky bit? Where have you seen them?
19. What is umask and why does it matter?
20. How do ACLs help in production environments?

---

## 5️⃣ Process & Resource Management (VERY IMPORTANT)

21. What is a process and how do you inspect it?
22. Process vs Thread (real-world example).
23. What is PID 1 and why is it critical?
24. Explain process states (R, S, D, Z).
25. Commands to monitor processes:

* `ps`
* `top`
* `htop`
* `atop`

26. What is load average and how do you interpret it?
27. How do you find which process is consuming high CPU or memory?

---

## 6️⃣ CPU, Memory & Limits (DevOps Core)

28. Difference between RAM and Swap.
29. What happens when memory is exhausted?
30. What is OOM Killer and how do you detect it?
31. How do you analyze high CPU usage in production?
32. What is `nice` and `renice`?
33. What is `ulimit`?

* Soft limit vs Hard limit
* Open files limit
* Process limit

34. How do you set `ulimit` permanently?
35. How does `ulimit` affect Docker containers?

---

## 7️⃣ Networking (Troubleshooting Focus)

36. Commands you use for network debugging:

* `ip`
* `ss`
* `netstat`
* `ping`
* `traceroute`
* `tcpdump`

37. TCP vs UDP (real-world use cases).
38. What is a port and how do you check open ports?
39. How do you check which process is using a port?
40. Linux firewalls:

* `iptables`
* `firewalld`
* `nftables`

41. How do you debug network latency issues?

---

## 8️⃣ Package & Service Management

42. Difference between `apt` and `yum/dnf`.
43. How do you install packages on servers without internet?
44. How do you rollback a broken package update?
45. How do you check service dependencies?

---

## 9️⃣ Logs, Metrics & Debugging

46. Where are system and application logs stored?
47. What is `journalctl` and how do you use it effectively?
48. How do you debug a service crash?
49. What is `logrotate` and why is it critical?
50. How do you correlate logs with metrics during incidents?

---

# 🔹 Scenario-Based Linux Interview Questions (DevOps Level)

---

## 🔥 Scenario 1: Server Not Reachable

**Q:**
Production server is not reachable via SSH. How do you debug?

**Expected areas:**

* Network connectivity
* Firewall
* SSH service
* Disk full
* CPU / memory saturation

---

## 🔥 Scenario 2: Disk 100% Full

**Q:**
Root filesystem is full. What are your immediate steps?

**Expected checks:**

* `df`
* `du`
* Log cleanup
* Docker volumes
* Cloud disk expansion

---

## 🔥 Scenario 3: High CPU Usage

**Q:**
CPU usage is constantly 100%. How do you troubleshoot?

**Expected tools:**

* `top`, `htop`
* `ps`
* Thread-level analysis
* Application logs

---

## 🔥 Scenario 4: High Memory Usage

**Q:**
An application is consuming excessive memory. How do you debug?

**Expected points:**

* `free`
* `vmstat`
* OOM logs
* Memory leaks

---

## 🔥 Scenario 5: Process Hanging

**Q:**
A process is stuck in `D` state. What do you do?

**Expected knowledge:**

* Process states
* Signals
* Kernel I/O wait

---

## 🔥 Scenario 6: systemd Service Failing

**Q:**
A service fails to start. How do you debug?

**Expected steps:**

* `systemctl status`
* `journalctl -xe`
* Unit file validation

---

## 🔥 Scenario 7: Permission Denied Error

**Q:**
Application fails with permission denied. How do you fix?

**Expected checks:**

* Ownership
* SELinux
* ACLs

---

## 🔥 Scenario 8: Network Latency

**Q:**
Application response time is slow. Network suspected.

**Expected tools:**

* `ping`
* `traceroute`
* `ss`
* MTU mismatch

---

## 🔥 Scenario 9: Container Issues

**Q:**
A Docker container crashes repeatedly. How do you debug?

**Expected checks:**

* Container logs
* Resource limits
* OOM kills
* `ulimit` inside container

---

## 🔥 Scenario 10: Zombie Processes

**Q:**
You see zombie processes. What does it mean and how do you fix?

**Expected understanding:**

* Parent process
* Signal handling
* Application bug

---

## 🔹 Advanced Linux (DevOps / SRE Level)

51. What is SELinux and how do you debug SELinux issues?
52. Difference between Enforcing, Permissive, Disabled.
53. What are cgroups and how do they relate to containers?
54. What are namespaces?
55. How does Linux enable container isolation?
56. What is `strace` and when do you use it?
57. What is `lsof` and real-world use cases?
58. How do you debug file descriptor leaks?
59. What tuning can be done via `/proc` and `/sys`?
60. How do you harden a Linux server for production?

---

