### Q1. A production server is slow, load average is very high, but CPU usage is low.
* When load average is high but CPU usage is low, it usually means processes are not CPU-bound — they are blocked waiting for something else.
* A process is ready to run, but it is blocked waiting for disk or network I/O, so it goes into D state. CPU may be idle, but the process cannot run until I/O completes.
OR
* The process is not getting CPU because it is waiting on I/O, not because CPU is busy.

---

### Q2. What is a thread?
* A thread is like a worker for a process.

---

### Q3. One process is consuming 100% CPU on a multi-core system. How do you:
* Identify the exact thread?
  * Simply use 'ps' command which lists the processes along with cpu.

---

### Q4. A server has free memory, but applications are still getting killed. Logs show:

```bash
Out of memory: Kill process xxxx
```
Why does this happen?
* A process can be killed even with free memory if it exceeds its cgroup or container memory limit, triggering the Linux OOM killer.

---

### Q5. What is cgroup?
* cgroup = control group in Linux.
* A cgroup is a Linux kernel feature that limits and isolates resources (CPU, memory, I/O) for a group of processes. Processes inside a cgroup cannot exceed these limits even if the host has free resources.

---

### Q6.How do you differentiate between:

CPU bottleneck
Memory bottleneck
Disk I/O bottleneck using Linux tools?

* A bottleneck is basically a limit.
* A bottleneck is the system resource that limits overall performance, like a CPU, memory, disk, or network limit.
* CPU bottleneck shows high CPU% and low I/O wait, memory bottleneck shows high swap usage and low free RAM, and disk I/O bottleneck shows high I/O wait, many D-state processes, and high disk utilization (%util).

---

### Q7. What is ulimit?
* ulimit is basically a usage limit.
* ulimit is a per-process usage limit that restricts how much of a resource (CPU, memory, open files, etc.) a process can consume.

What ulimit can do:

Limit the number of open files (-n) → yes
```bash
ulimit -n
1024
# Process cannot open more than 1024 files at once.
```

Limit max number of processes / threads (-u) → yes
```bash
ulimit -u
4096
# User cannot create more than 4096 processes/threads at the same time.
```

Limit stack size per thread (-s) → yes
Limit memory usage / virtual memory (-v) → yes
Limit core file size, CPU time, etc. → yes

ulimit -f

What it limits: Maximum size of a single file a process can create (in blocks, usually 512 bytes each).
```bash
ulimit -f
0
# 0 → cannot create any files
# 1000 → max file size ~512 KB × 1000 = 512 MB
```

---

### Q8: What is Soft limit vs Hard limit?
* The soft limit is the active limit a process uses and can be increased up to the hard limit, while the hard limit is the maximum ceiling and can only be changed by root
```bash
ulimit -Sn   # soft limit
ulimit -Hn   # hard limit
```

---

### Q9: What is Open Files Limit?
* The open files limit is a Linux resource limit that defines how many files a process (or user) can have open at the same time.

---

### Q10: What is OOM Killer?
OOM = Out Of Memory
OOM Killer is a Linux kernel mechanism that terminates processes when the system runs out of memory.
It tries to free memory to keep the system alive.

---

### Q11: What is nice and renice?
| Command  | Purpose                                   | When to Use                                                |
| -------- | ----------------------------------------- | ---------------------------------------------------------- |
| `nice`   | Start a process with a given CPU priority | When launching new CPU-heavy jobs                          |
| `renice` | Change priority of a running process      | To adjust priority of a running process without killing it |

---

### Q12: What happens when memory is exhausted?
* When memory is exhausted, the kernel frees caches, then OOM Killer kills processes to reclaim memory, and applications may crash if memory is unavailable

---

### Q13: Difference between RAM and Swap.
* RAM is fast memory for running programs, while swap is slower disk space used when RAM is full.
* In a VM, the memory you assign is RAM, and swap is disk space configured inside the VM to act as extra virtual memory

---

### Q14: What is load average and how do you interpret it?
* Load average shows the average number of processes waiting to run or running on CPU over a period of time.
* Linux reports 3 numbers: 1 min, 5 min, 15 min averages
*** You can find load average using:
```bash
uptime
 14:00:01 up 2 days,  3:20,  2 users,  load average: 2.50, 1.80, 1.20
```

---

### Q15: What is PID 1 and why is it critical?
* PID 1 is the first process started by the kernel, usually systemd, and it’s critical because it initializes the system, manages services, and reaps orphaned processes to prevent zombies.

---

### Q16: Process vs Thread (real-world example).
* A process is an independent program with its own memory, while threads are lightweight units inside a process that share memory and run concurrently.

---

### Q17: What is an inode?
* Inode = Index Node
* It is a data structure in a filesystem that stores metadata about a file, not the file content itself.

---

### Q18: Hard link vs Soft link (practical use cases).
Here’s a **simplified, interview-friendly version**:

---

## **Hard Link**

* Another name for the **same file** (shares inode)
* Cannot cross filesystems
* Original file deleted → hard link still works
* **Use case:** Backup or multiple references to same file

**Example:**

```bash
ln original.txt hardlink.txt
```

---

## **Soft Link (Symbolic Link)**

* **Shortcut or pointer** to the file (different inode)
* Can cross filesystems
* Original file deleted → link breaks
* **Use case:** Config files, shortcuts, long paths

**Example:**

```bash
ln -s /home/user/original.txt symlink.txt
```

---

### **One-liner**

> *“Hard link is another name for the same file, soft link is a shortcut pointing to the file path.”*

---

### Q19: A process keeps restarting automatically. How do you find who is restarting it?
* If a process keeps restarting, I check systemd service configuration, cron jobs, supervisor tools (like Docker or Kubernetes), custom scripts, and logs to identify who is automatically restarting it.

---

### Q20: How do you find the parent process of a running process? Why is this useful in debugging?
* The parent process of a running process can be found using ps, /proc/<pid>/status, or pstree, and it’s useful in debugging to identify who started the process, trace process chains, and investigate crashes or resource issues

---

### Q21: An application is running but users say it’s slow. Which process-level metrics do you check first and why?
* Check CPU & memory → identify resource hogs.
* Check I/O wait or disk usage → ensure it’s not blocked.
* Inspect threads and FD usage → check for leaks.
* Check process state → look for stuck processes (D state).

---

### Q22. How to limit CPU and memory usage for particular process on system?
* CPU and memory for Jenkins can be limited using systemd (MemoryMax and CPUQuota), cgroups (memory.limit_in_bytes and cpu.cfs_quota_us), or Docker flags (--memory and --cpus).

---

### Q23.
How does Docker use:

Namespaces
cgroups Explain with real examples.

* Docker uses namespaces to isolate containers from each other and the host, and cgroups to limit CPU, memory, and other resources for each container

---

### Q24. A container is OOM-killed even though the host has free memory. Why?
* Even if the host has free memory, a container can be OOM-killed because of memory limits enforced by cgroups.

---

### Q25.
How do you limit:

CPU
Memory
File descriptors for a Docker container?
* Docker uses cgroups to limit CPU and memory, and ulimit (exposed via --ulimit) to limit file descriptors. For CPU: --cpus; for memory: --memory; for open files: --ulimit nofile.

---

### Q26: A Docker container crashes repeatedly. How do you debug?
* Container logs, Resource limits, OOM kills, ulimit inside container

---

### Q27: Application response time is slow. Network suspected.
* ping, traceroute, ss

---

### Q28: A service fails to start. How do you debug?
* systemctl status, journalctl -xe

---

### Q29: An application is consuming excessive memory. How do you debug?
* free, vmstat, OOM logs, Memory leaks

---

### Q30: CPU usage is constantly 100%. How do you troubleshoot?
* top, htop, ps, Thread-level analysis, Application logs

---

### Q31: Root filesystem is full. What are your immediate steps?
* df, du, Log cleanup, Docker volumes, Cloud disk expansion

---

### Q32: Production server is not reachable via SSH. How do you debug?
* Network connectivity, Firewall, SSH service, Disk full, CPU / memory saturation

---

### Q33: Where are system and application logs stored?
* /var/log/---

---

### Q34: What is logrotate?
* logrotate is a Linux utility that automatically manages log files by:
> Rotating (renaming) logs
> Compressing old logs
> Deleting logs after a retention period
> Creating new log files

---

### Q35: Commands you use for network debugging:
ip: 
ss: 
netstat: 
ping: 
traceroute: 
tcpdump: 


Here are **single-line explanations with simple examples** (perfect for interviews 👌):

* **`ip`** – Used to view and configure network interfaces, IPs, and routes

  ```bash
  ip addr show
  ```

* **`ss`** – Displays active and listening TCP/UDP sockets

  ```bash
  ss -lntp
  ```

* **`netstat`** – Shows network connections and listening ports (legacy tool)

  ```bash
  netstat -tulnp
  ```

* **`ping`** – Tests if a host is reachable and measures latency

  ```bash
  ping google.com
  ```

* **`traceroute`** – Displays the network path (hops) to a destination

  ```bash
  traceroute google.com
  ```

* **`tcpdump`** – Captures and inspects network packets

  ```bash
  tcpdump -i eth0
  ```
---

### Q36: how to check running processes
ps aux: Lists all running processes, Shows who owns them, CPU/memory usage, and command.
ps -ef: Lists all running processes, Shows who started them and the parent process.

---

###  Q37: How to check which ports are occupied and which services are using them
Using ss (modern tool)
ss -tulnp: 
-t → TCP ports
-u → UDP ports
-l → listening ports
-n → show port numbers (not names)
-p → show the process using the port

Using netstat (older, still common)
sudo netstat -tulnp
Shows the same info: listening ports and processes using them.

Using lsof:
sudo lsof -i -P -n
Lists all open network connections
-i → network
-P → show port numbers
-n → don’t resolve hostnames

3. cpu/memory metrics
To check memory info:
free -h
cat /proc/meminfo

For CPU:
cat /proc/cpuinfo

real-time CPU + memory usage:
top/htop

---

### 38: how to check which process is using more cpu/memory
For real-time monitoring, top or htop is best.
For quick scripts or reporting, use ps aux --sort.

---

### 39: see logs of a service
journalctl -u <service_name>

---

### 40: check if a service is running or not
systemctl status service_name

---

### 41: Zombie process
A Zombie process (sometimes called a defunct process) is a process that has finished execution, but still remains in the process table because its parent process hasn’t read its exit status yet.
* A zombie process is a child process that has finished execution

---

### 42: Why Zombies Are Still a Problem?
Even though they don’t use CPU or RAM:
They consume PIDs
Too many zombies → PID exhaustion
New processes cannot be created

---

### 43: How to Fix Zombie Processes?

Fix the parent process:
Parent must call wait() / waitpid()

Restart the parent by
kill -9 <parent_pid>

Reboot (last option)

---

### 44: how to know the node installed path: 
```bash
whick node
```

---

### 45: Quick system summary
```bash
vmstat 2 5
# Shows CPU, memory, swap, and I/O every 2 seconds, 5 times
```

---

### 46: CPU load average: 
```bash
uptime
```

---

### **Basic process states (first character)**

| Code | Meaning                    | Description                                          |
| ---- | -------------------------- | ---------------------------------------------------- |
| `R`  | Running                    | Process is running or runnable (on CPU)              |
| `S`  | Sleeping                   | Waiting for an event (idle)                          |
| `D`  | Uninterruptible sleep      | Usually waiting for I/O (disk/network)               |
| `T`  | Stopped                    | Stopped by job control signal or debugger            |
| `Z`  | Zombie                     | Dead process, waiting for parent to read exit status |
| `I`  | Idle (Linux kernel thread) | Kernel thread not doing work                         |

---

### **Additional flags (following letters)**

| Letter | Meaning                                |
| ------ | -------------------------------------- |
| `<`    | High-priority process (nice < 0)       |
| `N`    | Low-priority process (nice > 0)        |
| `L`    | Has pages locked in memory (real-time) |
| `s`    | Session leader (controls a terminal)   |
| `+`    | In foreground process group            |

---

### **Examples from your list**

| STAT  | Meaning                                              |
| ----- | ---------------------------------------------------- |
| `S`   | Sleeping                                             |
| `Ss`  | Sleeping + session leader                            |
| `I<`  | Idle kernel thread + high priority                   |
| `SN`  | Sleeping + low priority                              |
| `Ssl` | Sleeping + session leader + has pages locked         |
| `I`   | Idle kernel thread                                   |
| `Ss+` | Sleeping + session leader + foreground process group |

---

✅ **Summary:**

* Most normal processes will show **S** (sleeping) when idle.
* **R** means actively using CPU.
* `I` is mostly kernel threads.
* Letters after the first give extra info about priority or session.

---

### 47: Command To check which IP address a domain name resolves to?
* nslookup domain.com
* dig domain.com(for detailed)
* host domain.com

---

### 48: 
Here’s a **quick, interview-ready answer**:

---

### **1️⃣ List number of CPUs (cores)**

```bash
nproc
```

* Shows the **number of processing units available**

Or more detailed:

```bash
lscpu
```

* Shows total CPUs, cores per socket, threads, architecture, etc.

---

### **2️⃣ List Kernel Version**

```bash
uname -r
```
* Shows **current running kernel version**

Or more detailed:

```bash
uname -a
```
* Shows kernel version, hostname, architecture, and more.

---


