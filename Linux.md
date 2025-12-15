Great 👍
For a **4+ years experienced DevOps engineer**, **Linux interviews** focus on **internals, troubleshooting, performance, and production scenarios**, not just basic commands.

Below is a **real interview–grade Linux question set**, split into **theoretical + scenario-based**, aligned with senior DevOps expectations.

---

# 🔹 Linux – Theoretical Interview Questions

## 1️⃣ Linux Fundamentals

1. What is Linux?
2. Difference between:

   * Linux vs Unix
   * Linux vs Windows (server perspective)
3. What is a **distribution**?
4. Common Linux distributions used in production.
5. What is the Linux kernel?

---

## 2️⃣ Linux Boot & System Initialization

6. Explain Linux boot process.
7. What is GRUB?
8. What is `systemd`?
9. Difference between:

   * `systemctl` and `service`
10. What are systemd targets?

---

## 3️⃣ Filesystem & Storage

11. Linux filesystem hierarchy (`/`, `/etc`, `/var`, `/usr`, `/opt`).
12. Difference between:

* `ext4`, `xfs`

13. What is inode?
14. Difference between:

* Hard link
* Soft link

15. What happens when disk is full?

---

## 4️⃣ File Permissions & Ownership

16. Explain Linux file permissions.
17. Difference between:

* `chmod`
* `chown`

18. What is SUID, SGID, Sticky bit?
19. How do umask values work?
20. How do ACLs work?

---

## 5️⃣ Process & Resource Management

21. What is a process?
22. Difference between:

* Process vs Thread

23. What is PID 1?
24. Explain process states.
25. Commands to monitor processes.
26. What is load average?

---

## 6️⃣ Memory & CPU Management

27. Difference between:

* RAM
* Swap

28. What happens when system runs out of memory?
29. What is OOM Killer?
30. How do you analyze high CPU usage?
31. What is nice and renice?

---

## 7️⃣ Networking

32. Basic networking commands:

* `ip`
* `ss`
* `netstat`
* `ping`
* `traceroute`

33. Difference between:

* TCP vs UDP

34. What is a port?
35. What is firewall in Linux?
36. `iptables` vs `firewalld` vs `nftables`

---

## 8️⃣ Package Management

37. Difference between:

* `apt`
* `yum` / `dnf`

38. How do you install software offline?
39. How do you rollback package upgrades?
40. What is a repository?

---

## 9️⃣ Logs & Monitoring

41. Where are system logs stored?
42. What is `journalctl`?
43. How do you rotate logs?
44. What is `logrotate`?
45. How do you troubleshoot service failures?

---

## 🔹 Scenario-Based Linux Interview Questions (VERY IMPORTANT)

---

## 🔥 Scenario 1: Server Not Reachable

**Q:**
A production server is not reachable over SSH. How do you debug?

**Expected areas:**

* Network connectivity
* Firewall rules
* SSH service
* Disk full
* CPU/memory saturation

---

## 🔥 Scenario 2: Disk Space Full

**Q:**
Server disk is 100% full. What do you do immediately?

**Expected steps:**

* Identify large files
* Clean logs
* Remove unused files
* Expand disk (cloud)

---

## 🔥 Scenario 3: High CPU Usage

**Q:**
Server CPU is constantly at 100%. How do you investigate?

**Expected tools:**

* `top`, `htop`
* `ps`
* Application logs
* Thread analysis

---

## 🔥 Scenario 4: High Memory Usage

**Q:**
Application is consuming too much memory. What do you do?

**Expected points:**

* `free`
* `vmstat`
* OOM events
* Memory leaks

---

## 🔥 Scenario 5: Process Hanging

**Q:**
A process is stuck and not responding. How do you handle it?

**Expected commands:**

* `kill`
* `kill -9`
* Signal understanding

---

## 🔥 Scenario 6: Service Not Starting

**Q:**
A systemd service fails to start. How do you debug?

**Expected steps:**

* `systemctl status`
* `journalctl`
* Unit file inspection

---

## 🔥 Scenario 7: Permission Denied Error

**Q:**
Application fails with “Permission denied”. How do you fix?

**Expected checks:**

* Ownership
* SELinux
* ACLs

---

## 🔥 Scenario 8: Network Latency

**Q:**
Application is slow due to network issues. How do you diagnose?

**Expected tools:**

* `ping`
* `traceroute`
* `ss`
* MTU issues

---

## 🔥 Scenario 9: Kernel Panic

**Q:**
A server crashes with kernel panic. What steps do you take?

**Expected actions:**

* Logs
* Hardware checks
* Kernel upgrade
* Rollback

---

## 🔥 Scenario 10: User Access Management

**Q:**
How do you manage user access securely on Linux servers?

**Expected answers:**

* SSH keys
* sudo rules
* PAM
* Audit logs

---

# 🔹 Advanced Linux Questions (4+ Years Level)

46. What is SELinux?
47. Difference between:

* Enforcing
* Permissive
* Disabled

48. What is cgroups?
49. What are namespaces?
50. How does Linux support containers?
51. What is strace?
52. What is lsof?
53. How do you debug zombie processes?
54. What is tuning via `/proc` and `/sys`?
55. How do you harden a Linux server?

---

# ✅ Interview Tip (Very Important)

At your experience level:

* Always explain **real incidents**
* Show **troubleshooting flow**
* Mention **security & performance**
* Be command-oriented but explain **why**

Example answer:

> “When we faced high CPU on a prod Linux server, we first checked top, identified a Java process, analyzed GC logs, and fixed the memory tuning…”

---

If you want next:

* 🔹 **Linux commands cheat sheet for interviews**
* 🔹 **Mock Linux troubleshooting interview**
* 🔹 **Linux + Docker + Kubernetes internals mapping**
* 🔹 **Production Linux hardening checklist**

Just tell me 👍
