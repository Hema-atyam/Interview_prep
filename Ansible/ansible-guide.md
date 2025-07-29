# ⚙️ Ansible Interview Prep – Beginner to Pro (With Real-Time Scenarios & Fixes)

---

## ✅ Section 1: Ansible Basics

### ❓ 1. What is Ansible?
Ansible is an open-source automation tool used for configuration management, application deployment, and task automation. It uses SSH and YAML-based playbooks.

---

### ❓ 2. How does Ansible work?
Ansible connects to target machines over SSH and uses modules to execute tasks defined in YAML playbooks.

---

### ❓ 3. What is an Inventory in Ansible?
An inventory is a file or script that defines the list of managed nodes. It can be static or dynamic.

```ini
[web]
192.168.1.10 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/id_rsa
```

---

### ❓ 4. What are Playbooks?
Playbooks are YAML files that define automation instructions like installing packages, starting services, or copying files.

```yaml
- name: Basic package install
  hosts: web
  become: true
  tasks:
    - name: Install Git
      yum:
        name: git
        state: present

```

---

### ❓ 5. What are Modules?
Modules are Ansible’s units of work — pre-written scripts that execute specific tasks like installing packages, copying files, managing users, etc.

```ansible all -i inventory.ini -m ping```

---

## ✅ Section 2: Real-Time Scenarios

### ❓ 6. Install Docker on Amazon Linux
Use a playbook to automate Docker installation across multiple EC2 instances, ensuring service is enabled and user is added to the Docker group.

```yaml
- name: Install Docker on Amazon Linux
  hosts: all
  become: true
  tasks:
    - name: Install Docker
      dnf:
        name: docker
        state: present

    - name: Enable Docker
      service:
        name: docker
        enabled: true
        state: started

    - name: Add user to docker group
      user:
        name: ec2-user
        groups: docker
        append: yes
```

---

### ❓ 7. Configure Nginx Using Templates
Deploy an Nginx configuration based on environment variables using Jinja2 templates and the `template` module. Notify handler to restart the service.
- **template/nginx.conf.j2:**

```
server {
    listen 80;
    server_name {{ ansible_hostname }};
    location / {
        root /usr/share/nginx/html;
    }
}
```

```yaml

- name: Configure Nginx
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present

    - name: Deploy Nginx config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: restart nginx

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

---

### ❓ 8. Mount EBS Volume
Automate mounting of an attached EBS volume using filesystem creation, mount module, and ensuring persistence via `/etc/fstab`.

```
- name: Mount EBS volume
  hosts: all
  become: true
  tasks:
    - name: Create filesystem
      filesystem:
        fstype: ext4
        dev: /dev/nvme1n1

    - name: Create mount point
      file:
        path: /data
        state: directory

    - name: Mount the volume
      mount:
        path: /data
        src: /dev/nvme1n1
        fstype: ext4
        state: mounted
        opts: defaults

```

---

### ❓ 9. Add User and Set SSH Key
Add a Linux user, set up their SSH authorized keys, and ensure proper ownership and permissions.

```
- name: Add DevOps user and setup SSH key
  hosts: all
  become: true
  tasks:
    - name: Create user
      user:
        name: devops
        shell: /bin/bash

    - name: Add SSH key
      authorized_key:
        user: devops
        key: "{{ lookup('file', 'devops_id_rsa.pub') }}"

```

---

## ✅ Section 3: Troubleshooting and Fixes

### ❓ 10. Host Unreachable
**Cause**: SSH failure or wrong inventory.  
**Fix**: Check host IPs, usernames, and SSH access.

`ansible all -m ping -i inventory.ini `

---

### ❓ 11. Permission Denied
**Cause**: Missing privilege escalation.  
**Fix**: Use `become: true`.

```
- name: Example with become
  hosts: all
  become: true
  tasks:
    - name: Install tree
      yum:
        name: tree
        state: present

```

---

### ❓ 12. Undefined Variables
**Cause**: Variable not passed via playbook or inventory.  
**Fix**: Define in `vars`, `group_vars`, or use `default()` filter.

```
- name: Use default to avoid undefined error
  debug:
    msg: "Region is {{ region | default('ap-south-1') }}"
```

---

### ❓ 13. Handlers Not Triggered
**Cause**: Missing `notify` in the task.  
**Fix**: Add `notify:` to tasks and define handler.

```
tasks:
  - name: Update config
    template:
      src: config.j2
      dest: /etc/myapp/config.conf
    notify: restart myapp

handlers:
  - name: restart myapp
    service:
      name: myapp
      state: restarted
```
---

### ❓ 14. Wrong Ownership/Permissions on Files
**Cause**: `owner`, `group`, or `mode` not set.  
**Fix**: Specify correct file ownership and permissions.

```
- name: Copy config with correct permissions
  copy:
    src: my.conf
    dest: /etc/my.conf
    owner: root
    group: root
    mode: '0644'
```

---

## ✅ Section 4: Advanced Concepts

### ❓ 15. `vars` vs `vars_files` vs `extra_vars`
- `vars`: Defined inline in the playbook.
- `vars_files`: Variables in external files.
- `extra_vars`: Passed using the `-e` flag, highest precedence.

```
vars:
  my_var: "from playbook"

vars_files:
  - vars/main.yml

```
**Command Line**

```
ansible-playbook playbook.yml -e "my_var=from_cli"
```
---

### ❓ 16. What Are Roles?
Roles are reusable, structured directories of tasks, handlers, templates, etc., that help organize large playbooks and promote reusability.
**Role Directory structure**

```
my-role/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
│   └── config.j2
├── vars/
│   └── main.yml

```


---

### ❓ 17. Difference Between block, rescue, always
- `block`: Primary task block.
- `rescue`: Executes on failure of the block.
- `always`: Executes no matter what.

```
tasks:
  - block:
      - name: Try something risky
        command: /bin/false
    rescue:
      - name: Handle failure
        debug:
          msg: "Failed!"
    always:
      - name: Always do this
        debug:
          msg: "Cleanup complete."

```

---

### ❓ 18. Managing Secrets With Ansible Vault
Use Vault to encrypt sensitive data like passwords, API keys, and certificates. Vault supports encrypting individual files, variables, or entire playbooks.

```
ansible-vault encrypt secrets.yml
ansible-vault view secrets.yml
ansible-playbook site.yml --ask-vault-pass

```
**Playbook Usage**

```
vars_files:
  - secrets.yml
```

---

### ❓ 19. Debugging Playbooks
Use verbosity flags or `debug` module to print variable values. Use `--start-at-task` to rerun from a specific task.

`ansible-playbook site.yml -vvv`

```
- name: Debug var
  debug:
    msg: "Value: {{ my_var }}"
```
---

### ❓ 20. Using Tags
Tags allow you to run specific parts of playbooks. Useful for targeting specific tasks or roles.

```
tasks:
  - name: Install nginx
    yum:
      name: nginx
      state: present
    tags: nginx

```
**Command**
`ansible-playbook site.yml --tags nginx`

---

## ✅ Section 5: Ansible Vault

### ❓ What is Ansible Vault?
Ansible Vault allows you to encrypt variable files or secrets so that sensitive data can be stored securely with your codebase.

---

### 🔐 Vault Use Cases:
- Encrypting AWS credentials, SSH private keys, DB passwords
- Keeping secrets secure in Git repositories
- Managing secrets in shared team environments

---

### 🧠 Best Practices:
- Store vault password securely (not in repo)
- Use vault IDs to manage multiple vaults
- Rotate secrets using `rekey`
- Use automation-friendly options like `--vault-password-file`

---

- **Encrypt a file:**
```bash
ansible-vault encrypt secrets.yml
```
- **Decrypt a file:**
```
ansible-vault decrypt secrets.yml
```
- **View an encrypted file without decrypting it:**
`ansible-vault view secrets.yml`
- **Edit an encrypted file:**

`ansible-vault edit secrets.yml`

- **Rekey (change the password) of an encrypted file:**
`ansible-vault rekey secrets.yml`
- **Create a new file and encrypt it on creation:**
`ansible-vault create secrets.yml`

- **🔐 Running a playbook that uses encrypted vars:**
Prompt for password:
`ansible-playbook playbook.yml --ask-vault-pass`

- **Use password file:**
`ansible-playbook playbook.yml --vault-password-file ~/.vault_pass.txt`

- **🔐 Sample vars_files usage in playbook:**
```
vars_files:
  - secrets.yml
```
Ensure secrets.yml is encrypted using vault.
---

## ✅ Section 6: Ansible Roles

### ❓ What Are Roles?
Roles break your automation into modular, reusable components. Each role contains folders like `tasks`, `handlers`, `templates`, `defaults`, `vars`, etc.

---

### 📁 Standard Role Structure:
- `defaults/`: default variables
- `vars/`: higher precedence variables
- `tasks/`: main playbook tasks
- `handlers/`: service restart/notify actions
- `templates/`: Jinja2 configuration files
- `files/`: static files
- `meta/`: role metadata
```
roles/
└── nginx_setup/
├── defaults/
│ └── main.yml
├── handlers/
│ └── main.yml
├── tasks/
│ └── main.yml
├── templates/
│ └── nginx.conf.j2
├── vars/
│ └── main.yml
├── files/
│ └── index.html
└── meta/
└── main.yml
```

---

### 🧪 Real-Time Role Examples:
- `nginx_setup`: install NGINX, configure templates, restart service
- `docker_setup`: install Docker, add user to group, start and enable Docker
- `efs_mount`: mount and persist EFS volumes across reboots

🔹 _**Add Role-Specific `tasks/main.yml` and `handlers/main.yml` Examples Here**_

**roles/nginx_setup/tasks/main.yml**
```yaml
- name: Install NGINX
  ansible.builtin.yum:
    name: nginx
    state: present

- name: Deploy NGINX configuration
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: restart nginx

- name: Ensure NGINX is started
  ansible.builtin.service:
    name: nginx
    state: started
```
**roles/nginx_setup/handlers/main.yml**

```
- name: restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```
** roles/docker_setup/tasks/main.yml:**
```
- name: Install Docker
  ansible.builtin.dnf:
    name: docker
    state: present

- name: Enable Docker
  ansible.builtin.service:
    name: docker
    state: started
    enabled: true

- name: Add user to docker group
  ansible.builtin.user:
    name: ec2-user
    groups: docker
    append: yes
```
---

### 🔁 Role Best Practices:
- One role per function (separation of concerns)
- Use `tags` and `when:` for control
- Store secrets in external encrypted files
- Use roles across multiple environments (dev, QA, prod)

---

## ✅ Section 7: Hands-On Practice

| Task                                      | Goal                               |
|-------------------------------------------|------------------------------------|
| Install Docker with Ansible               | Provision package + service        |
| Add SSH users from playbook               | User + security automation         |
| Mount EBS or EFS using playbook           | Filesystem automation              |
| Deploy NGINX with Jinja2 config template  | Config templating and handler use  |
| Encrypt and use secrets with Vault        | Secure automation                  |
| Create reusable role for a LAMP stack     | Role structure and modularity      |

- **✅ 1. Install Docker with Ansible**
```
- name: Install Docker using role
  hosts: all
  become: true
  roles:
    - docker_setup

```
- ** ✅ 2. Add SSH Users from Playbook**
```
- name: Create devops user with SSH access
  hosts: all
  become: true
  tasks:
    - name: Add user
      user:
        name: devops
        shell: /bin/bash

    - name: Add public key
      authorized_key:
        user: devops
        key: "{{ lookup('file', 'devops_id_rsa.pub') }}"
  ```
- **✅ 3. Mount EBS or EFS Using Playbook**
```
- name: Mount EBS Volume
  hosts: all
  become: true
  tasks:
    - name: Format volume
      filesystem:
        fstype: ext4
        dev: /dev/nvme1n1

    - name: Create mount point
      file:
        path: /data
        state: directory

    - name: Mount volume
      mount:
        path: /data
        src: /dev/nvme1n1
        fstype: ext4
        opts: defaults
        state: mounted

```
- ** ✅ 4. Deploy NGINX with Jinja2 Template**
```
- name: Deploy NGINX with templated config
  hosts: web
  become: true
  roles:
    - nginx_setup

```

- **✅ 5. Encrypt and Use Secrets with Vault
**Create vault file:**
```
ansible-vault encrypt group_vars/all/secrets.yml

```
** Playbook using it:** 
```
- name: Use secret in app config
  hosts: all
  become: true
  vars_files:
    - group_vars/all/secrets.yml
  tasks:
    - name: Render config
      template:
        src: app.conf.j2
        dest: /etc/myapp/app.conf

```

- ** ✅ 6. Create Reusable Role for LAMP Stack**
```
- name: Install full LAMP stack using roles
  hosts: all
  become: true
  roles:
    - apache
    - mysql
    - php

```
Each role ( `apache`, `mysql`, `php`) should have its own structured directory under `roles/`.



---


