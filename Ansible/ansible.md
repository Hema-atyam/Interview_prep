
---

## **1. Basic Interview Questions**

### **Q1. What is Ansible and why is it used?**

**Answer:**
Ansible is an **open-source IT automation tool** used for configuration management, application deployment, orchestration, and provisioning.
Key benefits:

* Agentless (uses SSH/WinRM)
* Declarative YAML syntax (easy to read/write)
* Idempotent (applies changes only when needed)

---

### **Q2. How is Ansible different from Puppet, Chef, or SaltStack?**

**Answer:**

* **Agentless:** No agent installation required (unlike Puppet/Chef)
* **Push-based:** Executes changes from a control node to managed nodes
* **Simple YAML:** Easy to learn, no complex DSL
* **Inventory-based:** Targets are defined in a simple INI/YAML inventory file

---

### **Q3. Explain Ansible architecture.**

**Answer:**

* **Control Node:** Where Ansible is installed & playbooks are executed.
* **Managed Nodes:** Target servers (no agent needed).
* **Inventory:** List of hosts/groups.
* **Modules:** Pre-built or custom tasks to perform actions.
* **Plugins:** Enhance functionality (e.g., connection, callback).
* **Playbooks:** YAML scripts defining automation tasks.

---

### **Q4. What is a playbook?**

**Answer:**
A playbook is a YAML file containing plays, and each play maps a group of hosts to tasks.

---

## **2. Intermediate Interview Questions**

### **Q5. How do you run an ad-hoc Ansible command?**

```bash
ansible all -i inventory.ini -m ping
ansible webservers -i inventory.ini -m shell -a "uptime"
```

---

### **Q6. What is an Ansible role?**

**Answer:**
Roles are a structured way of organizing playbooks into reusable components:

```
roles/
  apache/
    tasks/
    handlers/
    templates/
    vars/
    defaults/
```

---

### **Q7. How do you pass variables in Ansible?**

**Answer:**

* **Inventory vars**
* **vars: section in playbook**
* **Extra vars**:

```bash
ansible-playbook site.yml -e "user=hema env=prod"
```

---

### **Q8. What are Ansible facts?**

**Answer:**
Facts are system properties gathered from managed nodes.
Example:

```yaml
- debug:
    msg: "OS Family: {{ ansible_facts['os_family'] }}"
```

---

## **3. Advanced & Scenario-Based Questions**

### **Scenario 1 – Idempotency issue**

**Q:** Your Ansible playbook keeps running the same task on every execution even though nothing has changed. How do you fix it?
**Root Cause:** The module/task doesn’t support idempotency or you’re using `command` instead of an idempotent module.
**Fix:** Use modules like `file`, `yum`, `apt`, `copy`, `template` that ensure idempotency.

---

### **Scenario 2 – Host unreachable**

**Q:** You run `ansible all -m ping` but get `UNREACHABLE!` error.
**Root Cause:**

* SSH key not copied to the target
* Wrong inventory host/IP
* Firewall blocking SSH
  **Fix:**
* Ensure `ssh-copy-id user@host`
* Verify `ansible_user` and `ansible_host` in inventory
* Allow port 22 in firewall

---

### **Scenario 3 – Vault password issue**

**Q:** You get `ERROR! Decryption failed` while using Ansible Vault.
**Root Cause:** Wrong or missing vault password file.
**Fix:**

```bash
ansible-vault decrypt file.yml --ask-vault-pass
```

or

```bash
ansible-playbook site.yml --vault-password-file ~/.vault_pass.txt
```

---

### **Scenario 4 – Conditional execution**

**Q:** You only want to restart a service if a config file changes.
**Solution:**

```yaml
tasks:
  - name: Copy config
    copy:
      src: config.conf
      dest: /etc/myapp/config.conf
    notify: Restart myapp

handlers:
  - name: Restart myapp
    service:
      name: myapp
      state: restarted
```

---

### **Scenario 5 – Parallel execution**

**Q:** You want to speed up execution on 100+ servers.
**Fix:**

```bash
ansible-playbook site.yml -f 20
```

(`-f` controls the number of forks)

---

## **4. Real-Time Challenges in Ansible**

* **Slow execution** → Increase forks, use async & poll.
* **Different OS types** → Use `when` conditions and OS facts.
* **Managing secrets** → Use Ansible Vault.
* **Dry run testing** → Use `--check` mode.
* **Dynamic hosts (cloud)** → Use Dynamic Inventory plugins (AWS, Azure, GCP).

---

## **5. Hands-On Tasks They Might Ask**

1. **Write a playbook to install Apache and start the service**

```yaml
---
- hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present

    - name: Start Apache
      service:
        name: httpd
        state: started
        enabled: yes
```

2. **Write a playbook to create a user only if it doesn’t exist**

```yaml
- hosts: all
  become: yes
  tasks:
    - name: Ensure user exists
      user:
        name: hema
        state: present
```

3. **Ansible Vault usage**

```bash
ansible-vault create secrets.yml
ansible-vault edit secrets.yml
ansible-playbook site.yml --ask-vault-pass
```

---

## **6. Important Ansible Commands**

| Command                                      | Purpose                    |
| -------------------------------------------- | -------------------------- |
| `ansible --version`                          | Check version              |
| `ansible all -m ping`                        | Ping all hosts             |
| `ansible-playbook playbook.yml -i inventory` | Run a playbook             |
| `ansible-doc <module>`                       | Get module documentation   |
| `ansible-vault encrypt/decrypt`              | Encrypt/Decrypt files      |
| `ansible-galaxy init role_name`              | Create a role              |
| `ansible-lint playbook.yml`                  | Lint check for YAML syntax |

---

