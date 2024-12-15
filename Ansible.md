Here are **20 Ansible scenario-based interview questions and answers** to help you prepare for your interview:

---

### 1. **Scenario**: *You need to deploy a web server on multiple Linux machines using Ansible. How would you accomplish this?*

**Answer**:
To deploy a web server (e.g., Apache) on multiple Linux machines, you can write an Ansible playbook like this:

```yaml
---
- name: Deploy web server
  hosts: webservers
  become: true
  tasks:
    - name: Install Apache HTTP server
      apt:
        name: apache2
        state: present

    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: true
```

This playbook installs Apache and ensures the service is started and enabled on all hosts in the `webservers` group.

---

### 2. **Scenario**: *You want to manage different environments (e.g., development, staging, production) using Ansible. How would you handle this?*

**Answer**:
You can use Ansible **inventories** and **variable files** to manage different environments:

1. Create an inventory file for each environment (e.g., `dev.ini`, `staging.ini`, `prod.ini`).
2. Define environment-specific variables in group variable files (e.g., `group_vars/dev.yml`, `group_vars/staging.yml`).
3. Example command to run a playbook for a specific environment:
   ```bash
   ansible-playbook -i inventory/dev.ini playbook.yml
   ```

---

### 3. **Scenario**: *You need to install a specific version of a package using Ansible. How would you ensure the correct version is installed?*

**Answer**:
You can specify the version of the package in the `apt` or `yum` module. For example, to install version 2.4.6 of `nginx`:

```yaml
---
- name: Install specific version of nginx
  hosts: webservers
  become: true
  tasks:
    - name: Install nginx version 2.4.6
      apt:
        name: nginx=2.4.6-1
        state: present
```

The `apt` or `yum` module allows you to define the exact version you want to install.

---

### 4. **Scenario**: *You want to check if a file exists on a remote server and create it if it doesn't. How would you achieve this in Ansible?*

**Answer**:
You can use the `stat` module to check if a file exists and the `file` module to create it if necessary:

```yaml
---
- name: Ensure a file exists
  hosts: all
  tasks:
    - name: Check if the file exists
      stat:
        path: /path/to/file.txt
      register: file_stat

    - name: Create the file if it doesn't exist
      file:
        path: /path/to/file.txt
        state: touch
      when: not file_stat.stat.exists
```

This playbook will create the file only if it doesn’t already exist.

---

### 5. **Scenario**: *You need to restart a service on multiple machines only if the configuration file has changed. How would you ensure this?*

**Answer**:
You can use the `notify` directive in Ansible to trigger a handler only when a file changes:

```yaml
---
- name: Restart service if config changes
  hosts: webservers
  become: true
  tasks:
    - name: Update configuration file
      copy:
        src: /local/path/to/config
        dest: /etc/app/config
        notify: Restart app service

  handlers:
    - name: Restart app service
      service:
        name: app
        state: restarted
```

This playbook will only restart the service if the `config` file changes.

---

### 6. **Scenario**: *You want to run a task only on the first host in the inventory. How can you accomplish this in Ansible?*

**Answer**:
You can use the `ansible_play_batch` variable to run a task on the first host:

```yaml
---
- name: Run task on first host
  hosts: all
  tasks:
    - name: Run only on the first host
      debug:
        msg: "This task runs on the first host"
      when: ansible_play_batch[0] == inventory_hostname
```

This ensures that the task is executed only on the first host in the inventory.

---

### 7. **Scenario**: *You want to install and configure a MySQL server using Ansible. How would you do this?*

**Answer**:
You can use the `mysql_db` and `mysql_user` Ansible modules to manage MySQL installation and configuration:

```yaml
---
- name: Install and configure MySQL server
  hosts: dbservers
  become: true
  tasks:
    - name: Install MySQL server
      apt:
        name: mysql-server
        state: present

    - name: Ensure MySQL service is started
      service:
        name: mysql
        state: started
        enabled: true

    - name: Create a database
      mysql_db:
        name: mydatabase
        state: present

    - name: Create a MySQL user
      mysql_user:
        name: myuser
        password: mypassword
        priv: 'mydatabase.*:ALL'
        state: present
```

This will install MySQL, create a database, and a user with the necessary privileges.

---

### 8. **Scenario**: *You need to conditionally execute tasks based on the OS type (e.g., Debian vs RedHat). How would you do this?*

**Answer**:
You can use `when` conditions with Ansible facts to check the OS type:

```yaml
---
- name: Install package based on OS type
  hosts: all
  become: true
  tasks:
    - name: Install apache on Debian-based systems
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == 'Debian'

    - name: Install apache on RedHat-based systems
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == 'RedHat'
```

The `ansible_facts['os_family']` variable helps identify the OS family.

---

### 9. **Scenario**: *You want to install a package only if it is not already installed. How can you ensure this with Ansible?*

**Answer**:
Ansible's `apt` or `yum` modules are idempotent, so they only install the package if it is not already installed:

```yaml
---
- name: Install package only if not already installed
  hosts: all
  become: true
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

This playbook will only install `nginx` if it’s not already present.

---

### 10. **Scenario**: *You need to deploy a web application and set up a firewall rule to allow traffic on port 80. How would you do this using Ansible?*

**Answer**:
You can use the `ufw` module to configure firewall rules in addition to deploying the application:

```yaml
---
- name: Deploy web app and configure firewall
  hosts: webservers
  become: true
  tasks:
    - name: Install Apache web server
      apt:
        name: apache2
        state: present

    - name: Allow traffic on port 80
      ufw:
        rule: allow
        name: 'Apache'
        state: enabled
```

This playbook installs Apache and configures UFW to allow HTTP traffic on port 80.

---

### 11. **Scenario**: *You need to delete a file on a remote server only if it exists. How can you handle this in Ansible?*

**Answer**:
You can use the `file` module to remove the file conditionally:

```yaml
---
- name: Delete file if it exists
  hosts: all
  tasks:
    - name: Check if file exists
      stat:
        path: /path/to/file.txt
      register: file_stat

    - name: Remove the file if it exists
      file:
        path: /path/to/file.txt
        state: absent
      when: file_stat.stat.exists
```

This will delete the file only if it exists.

---

### 12. **Scenario**: *You need to create a user and set up a specific SSH key for that user on multiple remote servers. How would you do this?*

**Answer**:
You can use the `user` module to create the user and the `authorized_key` module to set up the SSH key:

```yaml
---
- name: Create user and set up SSH key
  hosts: all
  become: true
  tasks:
    - name: Create user
      user:
        name: newuser
        state: present

    - name: Add SSH key to user
      authorized_key:
        user: newuser
        state: present
        key: "{{ lookup('file', '/path/to/public/key.pub') }}"
```

This playbook creates a user and adds the SSH key for authentication.

---

### 13. **Scenario**: *You want to create a backup of a configuration file and ensure that it is kept up-to-date. How would you do this in Ansible?*



**Answer**:
You can use the `copy` module to back up a file with a timestamp:

```yaml
---
- name: Backup configuration file
  hosts: all
  become: true
  tasks:
    - name: Backup configuration file
      copy:
        src: /etc/app/config
        dest: /backups/config_{{ ansible_date_time.iso8601 }}.bak
        backup: yes
```

This will create a backup with a timestamp in the backup directory.

---

### 14. **Scenario**: *You need to synchronize files from one directory to another on multiple remote servers. Which Ansible module would you use?*

**Answer**:
You can use the `synchronize` module, which is based on `rsync`:

```yaml
---
- name: Synchronize files between directories
  hosts: all
  become: true
  tasks:
    - name: Synchronize files
      synchronize:
        src: /source/directory/
        dest: /destination/directory/
        recursive: yes
```

This playbook syncs files recursively between directories on the remote server.

---

### 15. **Scenario**: *You want to ensure a particular package is always up-to-date on all remote machines. How would you ensure this with Ansible?*

**Answer**:
You can use the `apt` or `yum` module with `state: latest` to always ensure the package is up-to-date:

```yaml
---
- name: Ensure package is up-to-date
  hosts: all
  become: true
  tasks:
    - name: Update nginx package
      apt:
        name: nginx
        state: latest
```

This will ensure `nginx` is always the latest version.

---

### 16. **Scenario**: *You need to deploy a database schema to multiple machines using Ansible. How would you handle this?*

**Answer**:
You can use the `mysql_db` module to deploy the schema:

```yaml
---
- name: Deploy database schema
  hosts: dbservers
  become: true
  tasks:
    - name: Import database schema
      mysql_db:
        name: mydatabase
        state: import
        target: /path/to/schema.sql
```

This playbook imports a schema into a MySQL database on multiple servers.

---

### 17. **Scenario**: *You need to apply the same configuration to multiple servers, but with different parameters for each server. How would you handle this in Ansible?*

**Answer**:
You can use **host_vars** or **group_vars** to store environment-specific parameters:

```yaml
---
- name: Apply different parameters to servers
  hosts: all
  tasks:
    - name: Apply server-specific configuration
      template:
        src: template.j2
        dest: /etc/config.conf
```

In `host_vars/` or `group_vars/`, define the parameters:

```yaml
# group_vars/webservers.yml
parameter_1: value1
parameter_2: value2
```

Ansible will automatically substitute values based on the inventory group.

---

### 18. **Scenario**: *You need to restrict access to a server so that only users in a specific group can SSH into it. How would you accomplish this in Ansible?*

**Answer**:
You can modify the `sshd_config` file and use the `lineinfile` module to restrict access:

```yaml
---
- name: Restrict SSH access by group
  hosts: all
  become: true
  tasks:
    - name: Allow only specific group to SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?AllowGroups'
        line: 'AllowGroups sshusers'
      notify:
        - Restart SSH service

  handlers:
    - name: Restart SSH service
      service:
        name: ssh
        state: restarted
```

This will ensure only users in the `sshusers` group can SSH into the server.

---

### 19. **Scenario**: *You need to install a specific version of a package (e.g., `nginx`) across multiple machines. How would you handle this in Ansible?*

**Answer**:
You can specify the version of the package when using the `apt` or `yum` module:

```yaml
---
- name: Install a specific version of nginx
  hosts: all
  become: true
  tasks:
    - name: Install nginx version 1.18
      apt:
        name: nginx=1.18.*
        state: present
```

This ensures only the specified version of `nginx` is installed.

---

### 20. **Scenario**: *You need to configure an NFS server to share directories across multiple machines. How would you handle this in Ansible?*

**Answer**:
You can use the `nfs` module and `mount` module to set up the NFS server:

```yaml
---
- name: Configure NFS server
  hosts: nfs_server
  become: true
  tasks:
    - name: Install NFS server package
      apt:
        name: nfs-kernel-server
        state: present

    - name: Configure NFS export
      lineinfile:
        path: /etc/exports
        line: '/data *(rw,sync,no_root_squash)'

    - name: Restart NFS service
      service:
        name: nfs-kernel-server
        state: restarted
```

This will configure the NFS server and restart the service after applying the changes.

---

These 20 scenario-based questions and answers will help you prepare for an Ansible-related interview by demonstrating how to handle real-world situations.
