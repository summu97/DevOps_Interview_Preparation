# 🔹 Ansible – Theoretical Interview Questions

## 1️⃣ Ansible Fundamentals

### 1. What is Ansible and why is it used?
---
### 2. Difference between:

   * Ansible vs Shell scripts

| Feature        | Ansible                    | Shell Scripts                  |
| -------------- | -------------------------- | ------------------------------ |
| Setup          | Agentless                  | Runs locally/on remote via SSH |
| Idempotency    | Yes                        | No (by default)                |
| Language       | YAML                       | Bash/Shell                     |
| Scalability    | High                       | Limited                        |
| Error Handling | Built-in                   | Manual                         |
| Use Case       | Configuration & automation | Simple tasks & scripts         |

   * Ansible vs Puppet/Chef

| Feature          | Ansible               | Puppet / Chef               |
| ---------------- | --------------------- | --------------------------- |
| Architecture     | Agentless             | Agent-based                 |
| Execution        | Push-based            | Pull-based                  |
| Learning Curve   | Easy                  | Steep                       |
| Configuration    | YAML                  | DSL (Ruby-based)            |
| Setup Complexity | Simple                | Complex                     |
| Best For         | Quick automation & CM | Large, complex environments |


---
### 3. What is **agentless architecture**?
---
### 4. How does Ansible communicate with managed nodes?
- Ansible communicates with managed nodes using SSH (for Linux/Unix) and WinRM (for Windows), without requiring any agent on the target machines.
---
### 5. What are Ansible **modules**?
- Ansible modules are reusable units of code that perform specific tasks on managed nodes, such as installing packages, managing files, or configuring services.
---

## 2️⃣ Ansible Architecture & Components

### 6. Explain:

   * Control node
   - Control Node: The machine where Ansible is installed and from which playbooks and commands are executed.

   * Managed nodes
   - Managed Nodes: The target machines that Ansible manages and configures using modules (accessed via SSH/WinRM).

---
### 7. What is an **inventory**?
- An inventory is a file that lists the managed nodes (hosts) and groups of hosts that Ansible manages.
---
### 8. Types of inventory:

   * Static
   * Dynamic
---
### 9. What is an **Ansible collection**?
- An Ansible collection is a bundle of Ansible content (modules, roles, plugins) distributed together for easy reuse and versioning.
---
### 10. What is the difference between **module** and **plugin**?
- Module: Performs tasks on managed nodes (e.g., install packages, copy files).
- Plugin: Extends Ansible’s core behavior (e.g., connection methods, callbacks, filters).Enhances how Ansible works internally without directly changing nodes.
---

## 3️⃣ Playbooks & YAML

### 11. What is an Ansible playbook?
- An Ansible playbook is a YAML file that defines a set of tasks to be executed on managed nodes to automate configuration and deployment.
---
### 12. Structure of a playbook.

```bash
- name: Install Git on target hosts
  hosts: all
  become: yes # Runs as root user

  tasks:
    - name: Install Git package
      apt: name=git state=present

```
---
### 13. Difference between:

* Playbook
- A YAML file that can contain one or more plays.

* Play
- A section inside a playbook that targets specific hosts and defines tasks to run on them.
- It can have one or more tasks each

```bash
- name: Install Git on target hosts
  hosts: all
  become: yes

  tasks:
    - name: Install Git package
      apt: name=git state=present

- name: Install Vim on target hosts
  hosts: all
  become: yes

  tasks:
    - name: Install Vim package
      apt: name=vim state=present
 # Here "name: Install Git on target hosts" and "name: Install Vim on target hosts" are plays

```

* Task
- A task in Ansible is a single action executed on a managed node.

```bash
- name: Install Development Tools on target hosts
  hosts: all
  become: yes

  tasks:
    - name: Install Git package # task1
      apt: name=git state=present

    - name: Install Vim package # task2
      apt: name=vim state=present

    - name: Install Curl package #task3
      apt: name=curl state=present
```

---
### 14. What is **idempotency** in Ansible?
- With idempotency, a task will not redo or change anything if the desired state is already achieved.

- Example:
  * Installing Git: If Git is already installed, Ansible won’t reinstall it.
  * Creating a file: If the file already exists with the correct content, no changes are made.
---
### 15. How does Ansible ensure idempotency?
- Ansible ensures idempotency by using modules that check the current state of the system before making changes.
  * Each module knows the desired state (e.g., package installed, service running).
  * Before performing any action, it compares the current state with the desired state.
  * If the system is already in the desired state, no changes are made.

- If Git is already installed, Ansible will skip the task and report “ok” instead of “changed”.
---

## 4️⃣ Variables & Facts

### 16. Types of variables in Ansible.

Here’s your **list updated with `#` comments for variables and explanations**:

---

Here’s a **list of Ansible variable types with examples**:

a. **Playbook Variables** – Defined inside a playbook under `vars:`

```yaml
- name: Example Playbook
  hosts: all
  vars:
    app_name: myapp   # variable defined in the playbook
  tasks:
    - debug:
        msg: "Application name is {{ app_name }}"
```

b. **Inventory Variables** – Defined for hosts or groups in inventory

```ini
[webservers]
web1 ansible_host=192.168.1.10 app_port=8080  # app_port is an inventory variable
```

c. **Facts / Registered Variables** – Collected using `setup` or `register`

```yaml
- name: Get hostname
  command: hostname
  register: host_name   # host_name stores the output of the command

- debug:
    msg: "Hostname is {{ host_name.stdout }}"
```

d. **Role Variables** – Defined inside roles in `defaults/main.yml` or `vars/main.yml`

```yaml
# roles/myrole/defaults/main.yml
app_port: 80   # role variable
```

e. **Extra Variables** – Passed at runtime with `-e`

```bash
ansible-playbook site.yml -e "app_name=myapp"  # app_name is an extra variable
```

f. **Prompted Variables** – Asked from user during execution

```yaml
vars_prompt:
  - name: "user_name"   # variable prompted from user
    prompt: "Enter your username"
```

g. **Environment Variables** – Read from the system environment

```yaml
- debug:
    msg: "Home directory is {{ lookup('env','HOME') }}"  # HOME is an environment variable
```

---
### 17. Variable precedence order (high-level).
Here’s a **high-level variable precedence order in Ansible** (from **lowest to highest priority**):

```bash
Lowest priority
Role default variables (roles/<role>/defaults/main.yml)
<
Inventory variables (inventory, host_vars, group_vars)
<
Playbook variables (vars:)
<
Registered variables (register)
<
Role variables (roles/<role>/vars/main.yml)
<
Prompted variables (vars_prompt)
<
Extra variables (ansible-playbook -e)
Highest priority

```

Short interview line ✅

> **Extra variables override all others; role defaults have the lowest priority.**
* **Extra vars (`-e`) override everything.**
* Defaults in roles are overridden by almost all other variable types.

---
### 18. What are **facts**?
- Defaut tak of Ansible. Gather_facts collects information about the managed nodes.
- The collected facts include:
  * IP addresses
  * Hostname
  * OS type and version
  * CPU, memory, disk details
  * Network interfaces
  * Environment variables
  * And much more
- A setting in a playbook that tells Ansible to collect system information from managed nodes at the start of a play.

```bash
- name: Gather node info example
  hosts: all
  gather_facts: yes

  tasks:
    - debug:
        msg: "IP: {{ ansible_facts['default_ipv4']['address'] }}, Hostname: {{ ansible_facts['hostname'] }}"

```

---
### 19. What is `ansible_facts`?
- It is the data collected from that action.
- A variable/dictionary that stores all the information collected by gather_facts.
- Access facts like ansible_facts['hostname'] or ansible_facts['default_ipv4']['address'] in tasks.

```bash
- name: Gather node info example
  hosts: all
  gather_facts: yes

  tasks:
    - debug:
        msg: "IP: {{ ansible_facts['default_ipv4']['address'] }}, Hostname: {{ ansible_facts['hostname'] }}"

```

---
### 20. How do you define custom facts?

You can define **custom facts** in Ansible in a few ways. Here’s a simple explanation:

---

### 1. **Using `set_fact` module (most common)**

* Defines a variable at runtime in a playbook.
* Stored in memory for that play.

```yaml
- name: Define custom facts using set_fact
  hosts: all
  tasks:
    - name: Set custom fact
      set_fact:
        app_version: "1.0.0"

    - debug:
        msg: "Application version is {{ app_version }}"
```

---

### 2. **Using facts files on managed nodes**

* Place a YAML or script file in `/etc/ansible/facts.d/` on the managed node.
* Facts are automatically gathered by `gather_facts`.

**Example:**
File: `/etc/ansible/facts.d/myapp.fact`

```ini
[general]
app_name=MyApp
app_version=1.0.0
```

* After `gather_facts`, these will appear under `ansible_facts['ansible_local']`.

---

### 3. **Using role defaults or vars**

* You can also define “custom facts” as variables in **roles** or **playbooks** for dynamic use.

---

* Example:

```yaml
- name: Custom facts example
  hosts: all
  gather_facts: yes

  tasks:
    - name: Set custom fact
      set_fact:
        app_version: "1.0.0"

    - name: Install app version 1.0.0
      debug:
        msg: "Installing App version {{ app_version }}"
      when: app_version == "1.0.0"

    - name: Skip installation for other versions
      debug:
        msg: "Skipping installation, version is not 1.0.0"
      when: app_version != "1.0.0"
```

✅ **Explanation:**

1. `set_fact` creates a **custom fact** `app_version`.
2. The `when` condition uses this custom fact to decide **which tasks to run**.
3. This makes playbooks **dynamic and flexible** based on node-specific data.

You can also use **custom facts from `/etc/ansible/facts.d/`** in the same way with `ansible_facts['ansible_local']`.

---

## 5️⃣ Roles & Reusability

### 21. What is an Ansible role?
- An Ansible role is a predefined, reusable set of tasks, variables, files, templates, and handlers that can be included in playbooks to automate a specific function or configuration.
  * Roles help organize playbooks and promote reusability.
  * They follow a standard directory structure.

```bash
myrole/
├── tasks/
│   └── main.yml        # Tasks to execute
├── handlers/
│   └── main.yml        # Handlers (e.g., service restart)
├── vars/
│   └── main.yml        # Variables specific to the role
├── defaults/
│   └── main.yml        # Default variables
├── files/              # Static files to copy
├── templates/          # Jinja2 templates
└── meta/
    └── main.yml        # Role metadata

```

---

* Usage in a playbook:

```bash
- name: Apply myrole to webservers
  hosts: webservers
  roles:
    - myrole
```

---
### 22. Standard role directory structure.


```
install_ubuntu_desktop/
├── tasks/
│   └── main.yml        # Tasks to install Ubuntu Desktop packages
├── handlers/
│   └── main.yml        # Handlers like restart display manager if needed
├── vars/
│   └── main.yml        # Variables like package list or version
├── defaults/
│   └── main.yml        # Default variables (low priority)
├── files/              # Any files needed (e.g., config files)
├── templates/          # Templates (e.g., desktop config templates)
├── meta/
│   └── main.yml        # Metadata, dependencies, author info
└── README.md           # Optional documentation
```

**Example `tasks/main.yml`**:

```yaml
- name: Update apt cache
  apt:
    update_cache: yes

- name: Install Ubuntu Desktop
  apt:
    name: ubuntu-desktop
    state: present

- name: Install additional packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - gnome-tweaks
    - build-essential
```

---
### 23. Difference between:

* Role vs Playbook

- Playbook = “what to run”
- Role = “a reusable package of tasks and configs” that can be called inside a playbook.
---
### 24. How do you share roles across projects?

You can **share Ansible roles across projects** in several ways:

---

### 1. **Using `roles_path` in `ansible.cfg`**

* Set a common directory where roles are stored.
* All playbooks can access roles from that path.

```ini
# ansible.cfg
[defaults]
roles_path = /opt/ansible/roles:/home/user/roles
```

* Now playbooks can use:

```yaml
roles:
  - myrole
```

---

### 2. **Using `requirements.yml` with Ansible Galaxy**

* Create a `requirements.yml` listing roles to install.
* Install roles for the project using:

```yaml
# requirements.yml
- src: git+https://github.com/username/myrole.git
  version: main
  name: myrole
```

```bash
ansible-galaxy install -r requirements.yml
```

* This makes the role **available for multiple projects**.

---

### 3. **Copying / Cloning roles**

* You can **copy or clone the role directory** into each project’s `roles/` folder.
* Simple but **less maintainable** compared to `roles_path` or Galaxy.

---

✅ **Best practice:**
* Use **Ansible Galaxy / git + requirements.yml** or a **shared roles path** to avoid duplication and ensure consistency.

---
### 25. What is  `import_role` vs `include_role` ?

* **`import_role`**: Loads the role **statically at playbook parsing**.

  * All tasks from the role are known **before execution**.
  * Cannot use `when` conditions dynamically.

* **`include_role`**: Loads the role **dynamically at runtime**.

  * Can be used with `when`, `with_items`, or loops.
  * Useful when you want the role to run **only under certain conditions**.

**Example:**

```yaml
# Static role inclusion
- import_role:
    name: common  # always runs

# Dynamic role inclusion
- include_role:
    name: webserver
  when: ansible_facts['os_family'] == "RedHat"  # runs only on RedHat hosts
```

✅ **Tip:** Use `import_role` for roles always needed, `include_role` for conditional or looped roles.


### What is **parse time**?
Exactly ✅

* **Parse time** happens **before the playbook starts executing on any host**.
* Ansible reads the playbook, loads all static includes/imports, checks syntax, and prepares the tasks.
* After that, **runtime** begins, where tasks are actually executed on the managed nodes.

**Example:**

```yaml
- import_role:
    name: common   # loaded at parse time, before execution

- include_role:
    name: webserver
  when: ansible_facts['os_family'] == "RedHat"  # evaluated at runtime
```

So, parse time = **planning phase**, runtime = **execution phase**.

---

## 6️⃣ Inventory & Grouping

### 26. How do you group hosts?
- In Ansible, you can **group hosts** in the **inventory file** so you can target multiple hosts together in a playbook.

### 1. **Using Group Names in Inventory**

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[dbservers]
db1 ansible_host=192.168.1.20
db2 ansible_host=192.168.1.21
```

* `webservers` and `dbservers` are **groups**.
* You can target a group in a playbook:

```yaml
- name: Install Apache on web servers
  hosts: webservers
  tasks:
    - apt:
        name: apache2
        state: present
```

---

### 2. **Nested Groups**

```ini
[allservers:children]
webservers
dbservers
```

* `allservers` includes both `webservers` and `dbservers`.

---

### 3. **Assign Variables to Groups**

```ini
[webservers:vars]
http_port=80
```

* Variables set here apply to all hosts in the `webservers` group.

✅ **Key points:**

* Groups make it easier to **target multiple hosts** together.
* You can also create **nested groups** and assign **group-specific variables**.

---
### 27. What is `group_vars` and `host_vars`?
In Ansible:

* **`group_vars`** → Store variables for a **group of hosts**. All hosts in that group can access these variables.
* **`host_vars`** → Store variables for a **specific host**. Only that host can access its variables.

### Example Directory Structure

```
inventory/
├── hosts
├── group_vars/
│   └── webservers.yml
└── host_vars/
    └── web1.yml
```

### Example `group_vars/webservers.yml`

```yaml
http_port: 80
max_clients: 200
```

* All hosts in the `webservers` group will have `http_port` and `max_clients` variables.

### Example `host_vars/web1.yml`

```yaml
ansible_user: ubuntu
db_password: secret123
```

* Only the host `web1` will have these variables.

✅ **Key points:**

* `group_vars` = variables for multiple hosts in a group
* `host_vars` = variables for a single host
* Makes variable management **organized and reusable**.

---
### 28. How do you manage multiple environments (dev/prod)?
In Ansible, you can manage **multiple environments** (like dev, test, prod) using **separate inventories, group_vars, and playbooks**. Here’s a simple approach:

---

### 1. **Separate Inventory Files**

Create different inventory files for each environment:

```
inventories/
├── dev
│   └── hosts
├── prod
│   └── hosts
```

Example `inventories/dev/hosts`:

```ini
[webservers]
dev-web1 ansible_host=10.0.0.10
dev-web2 ansible_host=10.0.0.11
```

Example `inventories/prod/hosts`:

```ini
[webservers]
prod-web1 ansible_host=10.0.1.10
prod-web2 ansible_host=10.0.1.11
```

---

### 2. **Environment-Specific Variables**

Use `group_vars` or `host_vars` inside each inventory folder:

```
inventories/dev/group_vars/webservers.yml
inventories/prod/group_vars/webservers.yml
```

Example `group_vars/webservers.yml`:

```yaml
http_port: 8080   # dev port
```

```yaml
# prod/group_vars/webservers.yml
http_port: 80     # prod port
```

---

### 3. **Run Playbooks with Specific Inventory**

```bash
ansible-playbook -i inventories/dev/hosts site.yml
ansible-playbook -i inventories/prod/hosts site.yml
```

---

### 4. **Optional: Use Environment Variables**

Pass environment name as an extra variable:

```bash
ansible-playbook -i inventories/hosts site.yml -e "env=dev"
```

And in your playbook, you can use:

```yaml
- debug: msg="Deploying to {{ env }} environment"
```

✅ **Key points:**

* Separate **inventory files** per environment
* Separate **group_vars / host_vars** for environment-specific settings
* Use **extra variables** or tags for dynamic behavior

This keeps **dev, test, and prod isolated** while reusing the same playbooks.

---
### 29. What is a dynamic inventory and when would you use it?
- Static inventory → manually maintained, fixed hosts
- Dynamic inventory → automatically generated from external sources, ideal for dynamic or cloud environments

**Dynamic Inventory** in Ansible is a way to **automatically generate the list of hosts and groups** from an external source instead of a static inventory file.

---

### Key Points:

* **Dynamic inventory** fetches hosts from sources like:

  * Cloud providers: AWS, Azure, GCP, OpenStack
  * CMDB or databases
  * Scripts or APIs
* It outputs hosts and groups in **JSON format** that Ansible can read.

---

### When to use Dynamic Inventory:

* When your infrastructure is **changing frequently** (servers come and go).
* When you manage **cloud environments** with auto-scaling instances.
* To avoid **manually updating static inventory files**.

---
### 30. How does Ansible fetch cloud inventories?
Ansible fetches cloud inventories using **dynamic inventory plugins** that connect to cloud provider APIs.

**How it works (simple):**

* Ansible uses **inventory plugins** (AWS, Azure, GCP, etc.).
* These plugins **authenticate** to the cloud using credentials (IAM, service accounts, keys).
* The plugin **calls the cloud provider API**.
* It fetches **running instances/VMs**, their metadata (IP, tags, labels).
* Ansible **automatically groups hosts** based on tags/labels and regions.

**Examples:**

* AWS → `aws_ec2` inventory plugin
* Azure → `azure_rm` inventory plugin
* GCP → `gcp_compute` inventory plugin

**Example command:**

```bash
ansible-playbook -i aws_ec2.yml site.yml
```

**In short:**
Ansible talks to the **cloud API via inventory plugins**, pulls VM details dynamically, and builds the inventory at runtime.

---

## 7️⃣ Handlers, Tags & Conditionals

### 31. What is a handler?
A **handler** in Ansible is a **special task that runs only when notified by another task**.

* Used mainly for actions like **restarting or reloading services**.
* A handler runs **once**, even if multiple tasks notify it.

**Example:**

```yaml
tasks:
  - name: Update nginx config
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Restart nginx

handlers:
  - name: Restart nginx
    service:
      name: nginx
      state: restarted
```

**In simple terms:**

* **Task** makes a change
* **Handler** reacts to that change when notified

---
### 32. When are handlers executed?

* A **handler runs only when a task notifies it**.
* If no task triggers (notifies) the handler, it **will not execute**.

**In short:**
Task → **notify handler** → Handler runs (at end of the play).

---
### 33. What are tags and why are they useful?
**Tags** in Ansible are **labels** you assign to tasks, plays, or roles.

**Why they are useful:**

* Let you **run only specific parts** of a playbook.
* Help in **skipping or targeting tasks** without modifying the playbook.
* Useful for **debugging, partial deployments, or faster runs**.

**Example:**

```yaml
tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present
    tags: install

  - name: Start nginx
    service:
      name: nginx
      state: started
    tags: start
```

Run only install tasks:

```bash
ansible-playbook site.yml --tags install
```

Skip a tag:

```bash
ansible-playbook site.yml --skip-tags start
```

**In short:**
Tags help you **control what runs and when**, without changing the playbook.

---
### 34. What is `when` condition?
`when` is a **conditional statement** in Ansible.

It tells Ansible to **run a task only if a condition is true**.

**Example:**

```yaml
- name: Install nginx only on Ubuntu
  apt:
    name: nginx
    state: present
  when: ansible_facts['os_family'] == "Debian"
```

**In simple words:**
`when` = **IF condition for tasks**
Task runs **only when the condition matches**.

---
### 35. What is `register` and how is it used?
`register` is used to **store the output of a task in a variable** so you can **use it later** in the playbook.

**Example:**

```yaml
- name: Check disk usage
  command: df -h
  register: disk_output

- name: Show disk usage
  debug:
    msg: "{{ disk_output.stdout }}"
```

**How it’s used:**

* Captures command/module output
* Stores it as a variable
* Used in `when`, `debug`, or other tasks

**In simple terms:**
`register` = **save task result → reuse later**

---

## 8️⃣ Templates & Files

### 36. What is Jinja2?
**Jinja2** is a **templating engine** used by Ansible.

It allows you to **insert variables, conditions, and loops** inside files or playbooks.

**Example:**

```yaml
name: {{ app_name }}
```
In this example:

* `{{ app_name }}` is **Jinja2 syntax**
* Jinja2 **replaces `{{ app_name }}` with the actual value** of the variable at runtime

**Example:**

```yaml
vars:
  app_name: nginx
```

Becomes:

```yaml
name: nginx
```

**Where it’s used:**

* Templates (`.j2` files)
* Variables
* Conditionals (`if`)
* Loops (`for`)

**In simple terms:**
Jinja2 lets you create **dynamic and reusable configurations** instead of hard-coding values.

---
### 37. Difference between:

* `template`
* `copy`

**Difference between `template` and `copy` (simple):**

| `template`                               | `copy`                     |
| ---------------------------------------- | -------------------------- |
| Uses **Jinja2 templates** (`.j2` files)  | Copies files **as-is**     |
| Can use **variables, conditions, loops** | Cannot use variables       |
| File content is **dynamic**              | File content is **static** |
| Used for config files                    | Used for plain files       |

**Example:**

`template`:

```yaml
- template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

`copy`:

```yaml
- copy:
    src: file.txt
    dest: /tmp/file.txt
```

**In short:**

* Use `template` → when values change per host
* Use `copy` → when file is same everywhere

---
### 38. How do you loop in Ansible?
You loop in Ansible using **`loop`** (most common).

**Example:**

```yaml
- name: Install multiple packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - vim
    - curl
```

**Other ways (older):**

```yaml
with_items:
  - git
  - vim
```

**In simple terms:**
`loop` runs the **same task multiple times** with different values.


---
### 39. What is `with_items` vs `loop`?
**Difference between `with_items` and `loop` (simple):**

* **`with_items`**

  * Older looping method
  * Still works but **deprecated**
  * Limited features

* **`loop`**

  * New and **recommended**
  * More powerful and flexible
  * Works with complex data (lists, dicts)

**Example:**

`with_items`:

```yaml
with_items:
  - git
  - vim
```

`loop`:

```yaml
loop:
  - git
  - vim
```

**In short:**
Use **`loop`** → modern, flexible
Avoid **`with_items`** → legacy syntax

---
### 40. How do you manage config files dynamically?
You manage config files dynamically in Ansible using **templates (Jinja2)**.

**How it works (simple):**

* Create a `.j2` template file
* Use variables inside it
* Ansible replaces variables at runtime

**Example:**

`nginx.conf.j2`

```jinja2
server {
  listen {{ port }};
  server_name {{ server_name }};
}
```

Playbook:

```yaml
- template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

**In short:**
Dynamic configs = **template + variables + Jinja2**

---

## 9️⃣ Security & Vault

### 41. What is Ansible Vault?
**Ansible Vault** is used to **encrypt sensitive data** in Ansible.

**What it protects:**

* Passwords
* API keys
* Secrets
* Private variables

**Example:**

```bash
ansible-vault encrypt secrets.yml
```

Use in playbook:

```yaml
vars_files:
  - secrets.yml
```

Run playbook:

```bash
ansible-playbook site.yml --ask-vault-pass
```

**In simple terms:**
Ansible Vault keeps **secrets safe** while still using them in playbooks.

---
### 42. How do you encrypt variables?
You encrypt variables in Ansible using **Ansible Vault**.

**Ways to do it (simple):**

**1. Encrypt a variable file**

```bash
ansible-vault encrypt vars.yml
```

**2. Encrypt a single variable**

```bash
ansible-vault encrypt_string 'mypassword' --name 'db_password'
```

Output example:

```yaml
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          6638616466...
```

**3. Use the encrypted variable**

```yaml
- debug:
    msg: "{{ db_password }}"
```

**In short:**
Ansible Vault = **encrypt variables securely**

---
### 43. How do you rotate secrets?
In Ansible, **rotating secrets** means **updating or replacing sensitive values** (like passwords, API keys) securely. Here’s how you can do it:

---

### 1. **Update the Vault file**

* Edit the encrypted file with a new secret:

```bash
ansible-vault edit secrets.yml
```

* Change the value of the secret and save. Vault re-encrypts automatically.

---

### 2. **Use `encrypt_string` for new secrets**

* Generate a new encrypted string for a variable:

```bash
ansible-vault encrypt_string 'newpassword' --name 'db_password'
```

* Replace the old value in your vault file.

---

### 3. **Rekey the vault (optional)**

* If you want to **change the vault password** as well:

```bash
ansible-vault rekey secrets.yml
```

* Useful if the old password is compromised.

---

✅ **In short:**

* Editing the vault file = update secret
* `encrypt_string` = generate new secret
* `rekey` = rotate vault password

This keeps your secrets **secure and up-to-date**.

---
### 44. Can Vault be integrated with CI/CD?
Yes ✅, **Ansible Vault can be integrated with CI/CD pipelines** to manage secrets securely during automated deployments.

### How it works:

1. **Store encrypted files in your repository**

   * Keep `secrets.yml` or encrypted variables in Git.

2. **Pass Vault password securely**

   * Use CI/CD secrets or environment variables instead of hardcoding the password.
   * Example (Jenkins, GitHub Actions, GitLab CI):

```bash
ansible-playbook site.yml --vault-password-file /path/to/vault_pass.txt
```

* `/path/to/vault_pass.txt` can be a CI/CD secret injected at runtime.

3. **Use in playbooks**

```yaml
vars_files:
  - secrets.yml
```

4. **Rotate secrets automatically**

   * Update encrypted values in the pipeline using `ansible-vault encrypt_string` if needed.

---

**In short:**

* Store secrets in Vault
* Inject Vault password securely in CI/CD
* Use encrypted variables in playbooks during automated runs

This ensures **secrets are never exposed in logs or repositories**.

---
### 45. Why should secrets not be stored in plain YAML?
Secrets should **not be stored in plain YAML** because:

1. **Security risk** – Anyone with access to the repository or filesystem can read passwords, API keys, or sensitive data.
2. **Compliance issues** – Storing secrets in plain text may violate security policies or regulations.
3. **Accidental exposure** – Logs, backups, or shared files can leak secrets.
4. **No access control** – Plain YAML offers no encryption or protection; anyone can modify or misuse secrets.

**Solution:**
Use **Ansible Vault** or an external secrets manager (e.g., HashiCorp Vault, AWS Secrets Manager) to **encrypt and manage sensitive data safely**.

**In short:**
Plain YAML = **easy to leak**, Vault = **secure and controlled**.

---

## 🔹 Scenario-Based Ansible Interview Questions (VERY IMPORTANT)

---

## 🔥 Scenario 1: Idempotency Issue

**Q:**
A playbook runs successfully but changes resources every time. Why?

**Expected areas:**

* Module misuse
* Shell/command vs native modules
* `changed_when`
* Idempotent module selection

This happens because the playbook is **not idempotent**, so Ansible thinks changes are required every run. Common reasons:

---

### 1. **Module Misuse**

* Using a module incorrectly can make it **report changes even when nothing changed**.
* Example: `copy` with `force: yes` always updates the file.

---

### 2. **Using `shell` or `command` instead of native modules**

* Tasks using `shell` or `command` are **not automatically idempotent**.
* Example:

```yaml
- name: Create directory
  command: mkdir /tmp/test
```

* This will run every time, even if the directory exists.
* Better: use **native module**:

```yaml
- name: Create directory
  file:
    path: /tmp/test
    state: directory
```

---

### 3. **Missing or wrong `changed_when`**

* Some tasks may need **explicit conditions** to tell Ansible when to consider a change.
* Example:

```yaml
- name: Check something
  command: echo "test"
  changed_when: false  # prevents Ansible from marking as changed
```

---

### 4. **Non-idempotent module selection**

* Always prefer **idempotent modules** (e.g., `apt`, `yum`, `user`, `service`) instead of shell scripts.
* Non-idempotent modules or scripts **re-run every time**.

---

✅ **In short:**

* Use **idempotent modules**
* Avoid raw shell/command unless necessary
* Use **`changed_when`** to control reporting
* Misconfigured modules can trigger unnecessary changes


---

## 🔥 Scenario 2: Slow Playbook Execution

**Q:**
Ansible playbook is very slow. How do you optimize it?

**Expected answers:**

* Forks
* SSH pipelining
* Async & poll
* Reduce facts gathering

If an Ansible playbook is **running slow**, you can optimize it using several methods:

---

### 1. **Increase Forks**

* By default, Ansible runs **5 hosts in parallel**.
* Increase forks in `ansible.cfg` to run multiple hosts simultaneously:

```ini
[defaults]
forks = 20
```

---

### 2. **Enable SSH Pipelining**

* Reduces SSH overhead by **combining multiple SSH operations**.

```ini
[defaults]
pipelining = True
```

---

### 3. **Use Async & Poll**

* Run long-running tasks asynchronously to **avoid blocking other tasks**:

```yaml
- name: Run long task in background
  command: /path/to/long/script.sh
  async: 600
  poll: 0
```

* `poll: 0` → Ansible doesn’t wait for completion immediately

---

### 4. **Reduce Facts Gathering**

* By default, Ansible gathers facts for all hosts (can be slow).
* Disable or limit it if not needed:

```yaml
- hosts: all
  gather_facts: no
```

---

✅ **In short:**

* Increase **forks** → more parallelism
* Enable **pipelining** → faster SSH
* Use **async** → avoid blocking
* Reduce **facts gathering** → save time

---

## 🔥 Scenario 3: Multi-Environment Setup

**Q:**
How do you structure Ansible for dev, QA, and prod?

**Expected approach:**

* Separate inventories
* Group vars
* Role reuse
* CI/CD integration

Here’s a **simple way to structure Ansible for multiple environments (dev, QA, prod):**

---

### 1. **Separate Inventories**

* Create different inventory folders or files for each environment:

```
inventories/
├── dev/hosts
├── qa/hosts
└── prod/hosts
```

* Example `inventories/dev/hosts`:

```ini
[webservers]
dev-web1 ansible_host=10.0.0.10
dev-web2 ansible_host=10.0.0.11
```

---

### 2. **Group Vars**

* Define environment-specific variables under `group_vars` for each inventory:

```
inventories/dev/group_vars/webservers.yml
inventories/qa/group_vars/webservers.yml
inventories/prod/group_vars/webservers.yml
```

* Example `group_vars/webservers.yml`:

```yaml
http_port: 8080   # dev
```

```yaml
http_port: 80     # prod
```

---

### 3. **Role Reuse**

* Write **roles** for reusable tasks (install software, configure services).
* Call the same roles across all environments:

```yaml
roles:
  - common
  - webserver
```

---

### 4. **CI/CD Integration**

* Use the same playbooks in CI/CD pipelines.
* Pass the environment dynamically:

```bash
ansible-playbook -i inventories/dev/hosts site.yml
ansible-playbook -i inventories/prod/hosts site.yml
```

* Or use extra variables: `-e "env=dev"`

---

✅ **Summary:**

* **Inventories** → separate hosts per environment
* **Group vars** → environment-specific configs
* **Roles** → reusable tasks
* **CI/CD** → automated deployment across environments

This keeps **dev, QA, and prod isolated** while reusing playbooks efficiently.

---

## 🔥 Scenario 4: Conditional Deployment

**Q:**
Install software only on specific hosts.

**Expected tools:**

* Inventory groups
* `when`
* Host vars

To **install software only on specific hosts** in Ansible, you can use:

---

### 1. **Inventory Groups**

* Group hosts in the inventory:

```ini
[webservers]
web1 ansible_host=10.0.0.10
web2 ansible_host=10.0.0.11

[dbservers]
db1 ansible_host=10.0.1.10
```

* Target a group in the playbook:

```yaml
- name: Install Apache only on webservers
  hosts: webservers
  tasks:
    - apt:
        name: apache2
        state: present
```

---

### 2. **`when` Condition**

* Install based on variables or facts:

```yaml
- name: Install MySQL only on db1
  hosts: all
  tasks:
    - apt:
        name: mysql-server
        state: present
      when: inventory_hostname == "db1"
```

---

### 3. **Host Vars**

* Define host-specific variables:

```
host_vars/db1.yml
install_db: true
```

* Use it in the playbook:

```yaml
- name: Install MySQL if host has install_db=true
  apt:
    name: mysql-server
    state: present
  when: install_db
```

---

✅ **Summary:**

* **Inventory groups** → target multiple hosts easily
* **`when`** → conditional installation per host
* **Host vars** → store host-specific flags or settings

---

## 🔥 Scenario 5: Restart Service Only When Needed

**Q:**
How do you restart a service only if config changes?

**Expected answer:**

* Handlers
* Notify mechanism

To **restart a service only if the config changes**, use **handlers** and the **notify mechanism** in Ansible.

---

### Example:

```yaml
- name: Update Nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart Nginx   # Notify handler only if this task changes something

handlers:
  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```

**How it works:**

1. The task `Update Nginx config` runs.
2. If the config file is **modified**, it **notifies** the handler.
3. Handler `Restart Nginx` runs **once at the end of the play**.
4. If the file didn’t change, the handler **doesn’t run**.

---

✅ **Key points:**

* Use **handlers** for actions triggered by changes
* Use **notify** in tasks that may cause changes
* Ensures services **restart only when necessary**

---

## 🔥 Scenario 6: Secrets Exposure

**Q:**
How do you prevent secrets from being exposed in logs?

**Expected solutions:**

* `no_log: true`
* Vault
* CI masking

To **prevent secrets from being exposed in Ansible logs**, you can use:

---

### 1. **`no_log: true`**

* Hides task output in logs.

```yaml
- name: Run sensitive command
  command: some_secret_command
  no_log: true
```

---

### 2. **Ansible Vault**

* Encrypt passwords, API keys, and sensitive variables.

```yaml
vars_files:
  - secrets.yml
```

* Decrypt only at runtime.

---

### 3. **CI/CD Masking**

* Store secrets in **CI/CD secret managers** (Jenkins, GitHub Actions, GitLab).
* Ensure environment variables containing secrets are **masked in logs**.

---

✅ **Summary:**

* `no_log` → hides task output
* Vault → encrypts secrets in files
* CI masking → prevents secrets from appearing in pipeline logs

---

## 🔥 Scenario 7: Dynamic Inventory Failure

**Q:**
Dynamic inventory is not fetching hosts. How do you debug?

**Expected checks:**

* Credentials
* Plugin config
* API permissions
* Inventory script output

If a **dynamic inventory is not fetching hosts**, you can debug it by checking the following:

---

### 1. **Credentials**

* Make sure the credentials (API keys, service accounts, IAM roles) are **correct and active**.
* Example: AWS `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

---

### 2. **Plugin Configuration**

* Verify your **inventory plugin config** (`.yml`) is correct.
* Check variables like region, project, filters, etc.

```yaml
plugin: aws_ec2
regions:
  - us-east-1
```

---

### 3. **API Permissions**

* Ensure the account or service has **permissions to list hosts/instances**.
* Missing permissions can cause an empty inventory.

---

### 4. **Inventory Script / Plugin Output**

* Test the inventory manually:

```bash
ansible-inventory -i aws_ec2.yml --list
```

* Check if it returns hosts in **JSON format**.
* Any errors or empty output indicate misconfiguration.

---

✅ **Summary:**

* Check **credentials**, **plugin config**, **API permissions**, and **inventory output** to debug dynamic inventories.

---

## 🔥 Scenario 8: Partial Failure

**Q:**
Playbook fails on one host but succeeds on others. How do you handle it?

**Expected tools:**

* `ignore_errors`
* `max_fail_percentage`
* `serial`
* Error handling blocks

If a **playbook fails on one host but succeeds on others**, you can handle it in Ansible using:

---

### 1. **`ignore_errors`**

* Continue playbook execution even if a task fails on a host:

```yaml
- name: Install package
  apt:
    name: nginx
    state: present
  ignore_errors: yes
```

---

### 2. **`max_fail_percentage`**

* Stop the play only if a certain **percentage of hosts fail**:

```yaml
- hosts: all
  max_fail_percentage: 50
```

---

### 3. **`serial`**

* Run the play on **a few hosts at a time** instead of all simultaneously.
* Useful for rolling updates:

```yaml
- hosts: all
  serial: 2
```

---

### 4. **Error Handling Blocks**

* Use `block`, `rescue`, and `always` to handle failures gracefully:

```yaml
- block:
    - name: Run risky task
      command: /some/command
  rescue:
    - debug: msg="Task failed, handling error"
  always:
    - debug: msg="This runs regardless of success or failure"
```

---

✅ **Summary:**

* `ignore_errors` → continue on failure
* `max_fail_percentage` → tolerate limited failures
* `serial` → update hosts in batches
* `block/rescue/always` → handle errors cleanly

---

## 🔥 Scenario 9: Rolling Deployment

**Q:**
How do you deploy updates without downtime?

**Expected answer:**

* `serial`
* Health checks
* Load balancer draining

To **deploy updates without downtime**, you can use a combination of strategies in Ansible:

---

### 1. **Serial**

* Deploy updates to a **few hosts at a time** instead of all at once.
* Example (rolling update):

```yaml
- hosts: webservers
  serial: 2
  tasks:
    - name: Update application
      git:
        repo: https://github.com/myapp.git
        dest: /var/www/myapp
```

---

### 2. **Health Checks**

* Ensure updated hosts are **healthy before moving to next batch**.
* Example: check if service is running or response is OK:

```yaml
- name: Check service health
  uri:
    url: http://localhost:80/health
    status_code: 200
  register: health
  until: health.status == 200
  retries: 5
  delay: 10
```

---

### 3. **Load Balancer Draining**

* Remove the host from the **load balancer** before updating to prevent user traffic interruption.
* Add it back after update and health check pass.

```yaml
- name: Remove host from LB
  shell: /usr/local/bin/remove_from_lb.sh {{ inventory_hostname }}
```

---

✅ **Summary:**

* **Serial** → update hosts in batches
* **Health checks** → verify updates are successful
* **Load balancer draining** → prevent serving traffic during updates

This ensures **zero downtime deployments**.

---

## 🔥 Scenario 10: Configuration Drift

**Q:**
Servers drift from desired configuration. How do you fix and prevent it?

**Expected ideas:**

* Periodic runs
* Idempotent roles
* CI/CD enforcement

If servers **drift from the desired configuration**, you can fix and prevent it using:

---
- **Drift** is when a system’s **actual state differs from the state defined in your configuration management**.

**Example:**

* Desired state: `user1` exists, package `git` installed.
* Actual state: `user1` was deleted, `git` removed manually.
* The system has **drifted** from the intended configuration.

**In simple words:**

> Drift = the system is **out of sync** with what it should be.


### 1. **Periodic Runs**

* Schedule Ansible playbooks to run regularly (e.g., via **cron** or CI/CD pipelines) to enforce the correct state.
* Example cron job:

```bash
0 * * * * ansible-playbook /path/to/site.yml -i /path/to/inventory
```

---

### 2. **Idempotent Roles**

* Ensure all roles/tasks are **idempotent** so running them multiple times doesn’t cause unwanted changes.
* Example: use `state: present` for packages instead of `command: install`.

---

### 3. **CI/CD Enforcement**

* Integrate configuration management into **CI/CD pipelines**.
* Any deviation is automatically corrected during deployments.
* Example: pipeline triggers Ansible to **apply the desired state** after each commit or periodically.

---

✅ **Summary:**

* **Periodic runs** → auto-correct drift
* **Idempotent roles** → safe repeated runs
* **CI/CD enforcement** → ensure servers always match desired configuration

---

# 🔹 Advanced Ansible Questions (4+ Years Level)

### 46. Difference between:

* `include_tasks` vs `import_tasks`
---
### 47. What are Ansible callbacks?
**Ansible callbacks** are **plugins that run on specific events during playbook execution** and allow you to **customize output or trigger actions**.

---

### Key Points:

* They **react to events** like task start, task end, play start, play end, etc.
* Can be used for:

  * Logging
  * Sending notifications
  * Custom output formatting
* Default callback: `default` (normal console output)

---

### Example:

* Enable **Slack notifications** when a playbook finishes:

```ini
# ansible.cfg
[defaults]
callback_whitelist = slack
```

* Or enable **JSON output**:

```ini
[defaults]
stdout_callback = json
```

---

**In simple terms:**
Callbacks = **hooks triggered during playbook execution** to perform extra actions or customize output.

---
### 48. How does Ansible scale to thousands of nodes?
Ansible can **scale to thousands of nodes** using several mechanisms:

---

### 1. **Forks**

* By default, Ansible runs **5 hosts in parallel**.
* Increase `forks` in `ansible.cfg` to run more hosts simultaneously:

```ini
[defaults]
forks = 50
```

---

### 2. **Inventory Grouping**

* Organize hosts into **groups** to target them efficiently.
* Example: `webservers`, `dbservers`, `loadbalancers`.

---

### 3. **Serial Execution**

* Deploy in **batches** using `serial` to avoid overloading network or servers:

```yaml
- hosts: webservers
  serial: 100
```

---

### 4. **Asynchronous Tasks**

* Use `async` and `poll` for long-running tasks so they **don’t block other hosts**.

```yaml
- command: /long/script.sh
  async: 600
  poll: 0
```

---

### 5. **Pull Mode (Optional)**

* In extremely large environments, you can use **Ansible Pull** so each node pulls configuration instead of the control node pushing to all nodes.

---

✅ **Summary:**

* **Forks** → parallel execution
* **Groups & serial** → manage batches
* **Async** → non-blocking tasks
* **Pull mode** → distributed scaling

This allows Ansible to manage **thousands of nodes efficiently**.

---
### 49. What is Ansible Tower / AWX?
**Ansible Tower** (by Red Hat) and **AWX** (the open-source version) are **web-based interfaces and REST APIs for Ansible** that provide **enterprise features**.

---

### Key Features:

1. **Graphical UI** – Manage playbooks, inventories, and credentials visually.
2. **Role-based access control (RBAC)** – Control who can run or edit playbooks.
3. **Job scheduling** – Run playbooks automatically at specific times.
4. **Centralized logging** – Track execution history and output.
5. **REST API** – Integrate with other tools or CI/CD pipelines.
6. **Surveys** – Prompt users for inputs when launching playbooks.

---

**In simple terms:**

> Tower / AWX = **Ansible with a web interface, scheduling, and management for teams**.

* **AWX** = free/open-source
* **Tower** = commercial version with support and extra features

---
### 50. How do you implement RBAC in Ansible?
In Ansible Tower / AWX, **RBAC (Role-Based Access Control)** is used to **control who can access and perform actions** on inventories, projects, and job templates.

---

### How to implement RBAC:

1. **Define Users and Teams**

* Create **users** for individuals and **teams** for groups of users.
* Example: `DevOpsTeam`, `QATeam`.

2. **Create Organizations**

* Group projects, inventories, and credentials under **organizations**.

3. **Assign Roles**

* Assign **roles to users or teams** for specific resources:

| Role    | Description                     |
| ------- | ------------------------------- |
| Admin   | Full access to manage resources |
| Execute | Can run jobs only               |
| Read    | Can view but not modify         |

4. **Attach Roles to Resources**

* Example: Assign **Execute role** on a job template to QA team.
* Assign **Admin role** on inventory to DevOps team.

5. **Use Permissions in Workflows**

* Workflows respect these permissions automatically.
* Users can only run, edit, or view what their role allows.

---

**In simple terms:**
RBAC = **control who can do what** in Tower/AWX by assigning **roles to users or teams** on projects, inventories, or jobs.

---
### 51. How do you test Ansible roles?
You can **test Ansible roles** using several approaches to ensure they work correctly and are idempotent.

---

### 1. **Manual Playbook Testing**

* Write a simple playbook that calls the role and run it on a test environment:

```yaml
- hosts: test
  roles:
    - myrole
```

* Check if the role performs as expected.

---

### 2. **Use `ansible-playbook --check` (Dry Run)**

* Simulates changes without modifying the system:

```bash
ansible-playbook test.yml --check
```

* Helps verify idempotency.

---

### 3. **Use Molecule (Recommended)**

* Molecule is a **framework for testing roles**.
* Features:

  * Linting
  * Unit tests
  * Integration tests
  * Docker, Vagrant, or cloud instances for testing

**Basic Molecule workflow:**

```bash
molecule init role myrole
molecule create       # create test environment
molecule converge     # apply role
molecule verify       # run tests
molecule destroy      # clean up
```

---

### 4. **Idempotency Testing**

* Run the role **twice** on the same host:

  * First run applies changes
  * Second run should report **0 changes** if idempotent

---

✅ **Summary:**

* Test manually or with `--check`
* Use **Molecule** for automated, repeatable testing
* Ensure **idempotency** by running multiple times

---
### 52. What is Molecule?
**Molecule** is a **framework for testing and developing Ansible roles**.

---

### Key Points:

1. **Role Testing**

   * Allows you to test roles in **isolated environments** (Docker, Vagrant, cloud).
2. **Automated Tests**

   * Supports **linting**, **unit tests**, and **integration tests**.
3. **Idempotency Checks**

   * Ensures running a role multiple times doesn’t make unnecessary changes.
4. **Repeatable Environments**

   * Creates and destroys test instances automatically.

---

### Example Workflow:

```bash
molecule init role myrole   # create new role with molecule setup
molecule create             # spin up test environment
molecule converge           # apply role
molecule verify             # run tests
molecule destroy            # clean up
```

---

**In simple terms:**

> Molecule = **tool to safely test Ansible roles** before using them in production.

---
### 53. How do you handle Windows hosts in Ansible?
To handle **Windows hosts** in Ansible, you need to follow these steps because Windows doesn’t use SSH by default:

---

### 1. **Connection Setup**

* Use **`winrm`** instead of SSH:

```ini
[windows]
winhost1 ansible_host=192.168.1.10 ansible_user=Administrator ansible_password=Pass123 ansible_connection=winrm
```

---

### 2. **Enable WinRM on Windows Hosts**

* Run PowerShell commands to enable WinRM:

```powershell
winrm quickconfig
Set-Item WSMan:\localhost\Service\AllowUnencrypted $true
Set-Item WSMan:\localhost\Client\TrustedHosts * 
```

* Use HTTPS for secure connections in production.

---

### 3. **Use Windows Modules**

* Ansible has **Windows-specific modules** (all start with `win_`):

  * `win_service` → manage services
  * `win_feature` → install Windows features
  * `win_package` → install software
  * `win_copy` → copy files

**Example: Install IIS**

```yaml
- name: Install IIS
  hosts: windows
  tasks:
    - name: Install IIS Feature
      win_feature:
        name: Web-Server
        state: present
```

---

### 4. **Variables and Paths**

* Use Windows paths (`C:\path\to\file`)
* Use `win_shell` or `win_command` for PowerShell commands

---

✅ **In short:**

* Use **WinRM connection**
* Enable **WinRM on hosts**
* Use **Windows modules** (win_*) for tasks
* Paths and commands must match Windows conventions

---
### 54. How do you debug complex Ansible playbooks?
To **debug complex Ansible playbooks**, you can use several tools and techniques:

---

### 1. **`-v` / `-vv` / `-vvv` Flags**

* Run playbook with verbosity to get more details:

```bash
ansible-playbook site.yml -v    # basic info
ansible-playbook site.yml -vv   # more details
ansible-playbook site.yml -vvv  # full debug info
```

---

### 2. **`debug` Module**

* Print variables or messages during playbook execution:

```yaml
- name: Show variable value
  debug:
    msg: "App name is {{ app_name }}"
```

---

### 3. **`pause` / `prompt`**

* Pause execution to inspect or prompt user input:

```yaml
- pause:
    prompt: "Check this host before continuing"
```

---

### 4. **Check Idempotency**

* Run with `--check` (dry run) to see what would change:

```bash
ansible-playbook site.yml --check
```

---

### 5. **Use `ansible-playbook --start-at-task`**

* Start execution from a specific task to isolate problems:

```bash
ansible-playbook site.yml --start-at-task="Install Git package"
```

---

### 6. **Register Variables**

* Register variables are a way to capture the output of a task and store it in a variable so that you can use it later in the playbook.

```yaml
- name: Check disk space
  ansible.builtin.shell: df -h /
  register: disk_info

- name: Show the disk space output
  ansible.builtin.debug:
    msg: "{{ disk_info.stdout }}"

```

---

### 7. **Ansible Lint**

* Use **`ansible-lint`** to check for syntax issues, bad practices, and potential errors.
```bash
ansible-lint playbook.yaml
```
---

✅ **Summary:**

* Verbosity (`-vvv`) → see detailed execution
* `debug` → inspect variables
* `--check` → dry run
* `--start-at-task` → isolate tasks
* `register` → capture output
* Linting → detect errors early

This combination helps **debug even complex playbooks efficiently**.

---
### 55. How do you integrate Ansible with Terraform?
You can **integrate Ansible with Terraform** to automate infrastructure provisioning **and configuration management**.

---

### 1. **Terraform First, Ansible Next**

* **Terraform** provisions infrastructure (VMs, networks, load balancers).
* **Ansible** configures the resources (install software, deploy apps).

---

### 2. **Use Terraform Output as Inventory**

* Terraform can output host information in JSON:

```hcl
output "web_ips" {
  value = aws_instance.web.*.public_ip
}
```

* Save output to a file:

```bash
terraform output -json > inventory.json
```

* Use it in Ansible:

```yaml
- hosts: all
  vars_files:
    - inventory.json
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

---

### 3. **Dynamic Inventory Plugin**

* Ansible has a **Terraform inventory plugin**:

```yaml
plugin: community.general.terraform
directory: /path/to/terraform
```

* Automatically reads Terraform state and provides host info to Ansible.

---

### 4. **CI/CD Integration**

* Terraform and Ansible can be combined in a **pipeline**:

  1. Terraform `apply` → provision infrastructure
  2. Ansible → configure servers
  3. Optional tests → verify setup

---

✅ **Summary:**

* Terraform → provision resources
* Ansible → configure resources
* Use **Terraform outputs or dynamic inventory** to pass host info
* Integrate both in CI/CD for full automation

---
### 56. What is setup module and its use?

* The **`setup` module** in Ansible is used to **gather facts about remote hosts**.
* **Ansible automatically runs the `setup` module when `gather_facts` is enabled, so you don’t need to call it explicitly unless you’ve disabled fact gathering or want limited or refreshed facts.**
* By default, **Ansible automatically runs the `setup` module for you**.


### Key Points:

1. **Collects system information** like:

   * Hostname
   * IP addresses
   * OS type and version
   * Memory, CPU
   * Network interfaces

2. **Stores facts** in the variable `ansible_facts` so you can use them in playbooks.
## ✅ What actually happens?

* When Ansible runs the **`setup` module**
* Facts are:

  * Collected from the remote host
  * Stored **in memory**(temporary storage)
  * Available as variables under:

    ```yaml
    ansible_facts
    ```

Example:

```yaml
ansible_facts['hostname']
ansible_facts['os_family']
```

⚠️ **`ansible_facts` is a variable (logical dictionary), not a file.**


## NOTE:  ⚠️ Common Interview Trap

❌ *“setup is optional and not used by default”*
✅ *Correct answer:* **setup is used implicitly via `gather_facts`.**

## 🚫 When would you explicitly use `setup`?

Only in **specific DevOps scenarios**, such as:

### 1️⃣ You disabled fact gathering

```yaml
- hosts: all
  gather_facts: false
```

Later you need facts:

```yaml
- name: Manually gather facts
  ansible.builtin.setup:
```
### 2️⃣ Performance optimization (large infra)

Collect only what you need:

```yaml
- ansible.builtin.setup:
    gather_subset:
      - network
```
### 3️⃣ Debugging / learning purposes

```yaml
- ansible.builtin.setup:
```

(Useful to see **exactly what facts are collected**)

---

