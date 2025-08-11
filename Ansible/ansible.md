
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






---

## **Final 50 Ansible Interview Q\&A Cheat Sheet**

---

### **Section 1 – Basics (1–10)**

**Q1. What is Ansible?**
**A:** An open-source automation tool for configuration management, app deployment, orchestration, and provisioning.

* **Agentless**, uses **SSH/WinRM**
* Declarative **YAML** syntax
* Idempotent

---

**Q2. How is Ansible different from Puppet/Chef?**
**A:**

* No agent → simpler setup
* Push-based (control node initiates)
* YAML instead of Ruby/Puppet DSL

---

**Q3. Key Ansible components?**

* Control Node
* Managed Nodes
* Inventory
* Modules
* Plugins
* Playbooks

---

**Q4. Ad-hoc command to check connectivity?**

```bash
ansible all -i inventory.ini -m ping
```

---

**Q5. How to install a package using ad-hoc?**

```bash
ansible webservers -m yum -a "name=httpd state=present"
```

---

**Q6. What is a playbook?**

* YAML file containing plays → tasks → modules.

---

**Q7. Role directory structure?**

```
roles/
  myrole/
    tasks/
    handlers/
    templates/
    vars/
    defaults/
```

---

**Q8. How to pass variables?**

```bash
ansible-playbook site.yml -e "user=hema env=prod"
```

---

**Q9. What are Ansible facts?**

```yaml
- debug:
    msg: "{{ ansible_facts['distribution'] }}"
```

---

**Q10. Dry run mode?**

```bash
ansible-playbook site.yml --check
```

---

### **Section 2 – Intermediate (11–25)**

**Q11. How to run a playbook on specific group?**

```bash
ansible-playbook site.yml --limit webservers
```

---

**Q12. Ansible Vault usage?**

```bash
ansible-vault create secrets.yml
ansible-playbook site.yml --ask-vault-pass
```

---

**Q13. Template example:**

```yaml
- template:
    src: app.conf.j2
    dest: /etc/app/app.conf
```

---

**Q14. Conditional execution:**

```yaml
- yum:
    name: nginx
    state: present
  when: ansible_facts['os_family'] == "RedHat"
```

---

**Q15. Loop example:**

```yaml
- name: Create multiple users
  user:
    name: "{{ item }}"
    state: present
  loop:
    - user1
    - user2
```

---

**Q16. Notify handler only when file changes:**

```yaml
tasks:
  - copy:
      src: config.conf
      dest: /etc/app/config.conf
    notify: Restart app

handlers:
  - name: Restart app
    service:
      name: myapp
      state: restarted
```

---

**Q17. How to speed up execution?**

```bash
ansible-playbook site.yml -f 20
```

---

**Q18. Ignore failure for specific task:**

```yaml
- command: /bin/false
  ignore_errors: yes
```

---

**Q19. Limit execution to failed hosts:**

```bash
ansible-playbook site.yml --limit @/path/to/retry_file
```

---

**Q20. Tag usage:**

```yaml
- yum:
    name: git
    state: present
  tags: install
```

Run with:

```bash
ansible-playbook site.yml --tags install
```

---

**Q21. Gather facts disable:**

```yaml
- hosts: all
  gather_facts: no
```

---

**Q22. Copy file with permissions:**

```yaml
- copy:
    src: file.txt
    dest: /opt/file.txt
    mode: '0644'
```

---

**Q23. Install multiple packages:**

```yaml
- yum:
    name: "{{ item }}"
    state: present
  loop:
    - httpd
    - php
```

---

**Q24. How to fetch logs from remote to local?**

```yaml
- fetch:
    src: /var/log/messages
    dest: /tmp/logs/
```

---

**Q25. Environment variables in playbook:**

```yaml
- shell: echo $JAVA_HOME
  environment:
    JAVA_HOME: /usr/lib/jvm/java-8-openjdk
```

---

### **Section 3 – Advanced & Scenarios (26–50)**

**Q26. Scenario – Idempotency issue**
**Problem:** Playbook keeps making changes every run.
**Root Cause:** Using non-idempotent `command` or `shell`.
**Fix:** Use idempotent modules like `copy`, `yum`, `file`.

---

**Q27. Scenario – Host unreachable**
**Root Cause:** SSH key missing, wrong IP, firewall block.
**Fix:**

```bash
ssh-copy-id user@host
```

---

**Q28. Scenario – Vault password mismatch**
**Fix:** Use same vault password file for encrypt & decrypt.

---

**Q29. Scenario – Restart only if config changes** → Use handlers.

---

**Q30. Scenario – Parallel execution on 500 nodes** → Use `-f` forks.

---

**Q31. Scenario – Different OS types in same inventory** → Use `when` with `ansible_facts['os_family']`.

---

**Q32. Scenario – Manage secrets in Git** → Encrypt with Vault before pushing.

---

**Q33. Scenario – Run play only on subset** → Use `--limit`.

---

**Q34. Scenario – Skip tasks in test env** → Use `when: env != 'test'`.

---

**Q35. Scenario – Rollback changes** → Use `block` + `rescue`.

---

**Q36. Asynchronous execution example:**

```yaml
- command: /path/long_script.sh
  async: 300
  poll: 5
```

---

**Q37. Dynamic inventory with AWS:**

```bash
ansible-inventory -i aws_ec2.yml --graph
```

---

**Q38. Reuse code with include\_tasks:**

```yaml
- include_tasks: common.yml
```

---

**Q39. Create directories:**

```yaml
- file:
    path: /opt/data
    state: directory
    mode: '0755'
```

---

**Q40. Register variable and use it:**

```yaml
- command: uptime
  register: up

- debug:
    var: up.stdout
```

---

**Q41. Scenario – Fact caching** → Use `fact_caching = jsonfile` in ansible.cfg.

---

**Q42. Scenario – Jinja2 filter example:**

```yaml
- debug:
    msg: "{{ mylist | join(', ') }}"
```

---

**Q43. Scenario – Template with conditionals:**

```jinja2
{% if env == 'prod' %}
server_name prod.example.com;
{% else %}
server_name test.example.com;
{% endif %}
```

---

**Q44. Scenario – Run task only on first host:**

```yaml
when: inventory_hostname == groups['webservers'][0]
```

---

**Q45. Check syntax without running:**

```bash
ansible-playbook site.yml --syntax-check
```

---

**Q46. Lint playbook:**

```bash
ansible-lint site.yml
```

---

**Q47. Scenario – File ownership fix:**

```yaml
- file:
    path: /var/www/html
    owner: apache
    group: apache
```

---

**Q48. Scenario – Handlers from multiple roles** → Handlers merge automatically.

---

**Q49. Scenario – Zero downtime deployment** → Use serial batches:

```yaml
serial: 2
```

---

**Q50. Scenario – Testing playbook in CI/CD** → Use Molecule.

---


