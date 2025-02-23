In Ansible, **variables** and **facts** are essential for creating flexible and dynamic playbooks. 
Variables allow you to define reusable values, while facts provide information about the managed nodes (e.g., operating system, IP address, etc.).
Let’s dive into how to use variables and gather system facts in Ansible.

---

### **Variables in Ansible**

#### 1. **What Are Variables?**
Variables are used to store and reuse values in playbooks. They make playbooks more flexible and easier to maintain.

#### 2. **Defining Variables**
Variables can be defined in multiple places:
- **In Playbooks**:
  ```yaml
  vars:
    http_port: 80
  ```
- **In Inventory Files**:
  ```ini
  [webservers]
  server1 http_port=8080
  server2 http_port=80
  ```
- **In Separate Variable Files**:
  Create a file (e.g., `vars.yml`) and include it in the playbook:
  ```yaml
  vars_files:
    - vars.yml
  ```

#### 3. **Using Variables**
Variables are referenced using `{{ }}` syntax:
```yaml
tasks:
  - name: Open HTTP port
    firewalld:
      port: "{{ http_port }}/tcp"
      state: enabled
```

#### 4. **Special Variables**
- **`ansible_facts`**: Contains system facts gathered from the managed nodes.
- **`hostvars`**: Allows accessing variables of other hosts.
- **`group_names`**: Lists groups the current host belongs to.

---

### **Facts in Ansible**

#### 1. **What Are Facts?**
Facts are system-specific information gathered by Ansible from the managed nodes. Examples include:
- Operating system
- IP address
- CPU count
- Disk space

#### 2. **Gathering Facts**
Facts are automatically gathered by Ansible when a playbook runs. You can disable this behavior using:
```yaml
gather_facts: no
```

#### 3. **Accessing Facts**
Facts are stored in the `ansible_facts` variable and can be accessed using `{{ ansible_facts.<fact_name> }}`.

Example:
```yaml
- name: Print OS family
  debug:
    msg: "The OS family is {{ ansible_facts['os_family'] }}"
```

#### 4. **Common Facts**
Here are some commonly used facts:
- **`ansible_facts['os_family']`**: OS family (e.g., RedHat, Debian).
- **`ansible_facts['distribution']`**: OS distribution (e.g., CentOS, Ubuntu).
- **`ansible_facts['ipv4']['address']`**: IPv4 address.
- **`ansible_facts['memory']['total']`**: Total memory.
- **`ansible_facts['processor_count']`**: Number of CPUs.

---

### **Example Playbook Using Variables and Facts**

```yaml
---
- name: Configure webservers
  hosts: webservers
  remote_user: admin
  become: yes
  vars:
    http_port: 80
  tasks:
    - name: Print OS family
      debug:
        msg: "The OS family is {{ ansible_facts['os_family'] }}"

    - name: Install Apache (for RedHat family)
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"

    - name: Install Apache (for Debian family)
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == "Debian"

    - name: Start Apache service
      service:
        name: "{{ 'httpd' if ansible_facts['os_family'] == 'RedHat' else 'apache2' }}"
        state: started

    - name: Open HTTP port
      firewalld:
        port: "{{ http_port }}/tcp"
        state: enabled
```

---

### **Custom Facts**

#### 1. **Creating Custom Facts**
You can create custom facts by placing files in `/etc/ansible/facts.d/` on the managed nodes. These files should be in JSON or INI format.

Example (`/etc/ansible/facts.d/custom.fact`):
```json
{
  "application": {
    "name": "myapp",
    "version": "1.0"
  }
}
```

#### 2. **Accessing Custom Facts**
Custom facts are stored under `ansible_facts['ansible_local']`. For the above example:
```yaml
- name: Print custom fact
  debug:
    msg: "Application name is {{ ansible_facts['ansible_local']['custom']['application']['name'] }}"
```

---

### **Using Variables and Facts Together**

#### 1. **Dynamic Configuration**
Use facts to dynamically configure tasks. For example, install a package based on the OS:
```yaml
tasks:
  - name: Install Apache
    package:
      name: "{{ 'httpd' if ansible_facts['os_family'] == 'RedHat' else 'apache2' }}"
      state: present
```

#### 2. **Conditionals with Facts**
Use facts in conditionals to control task execution:
```yaml
tasks:
  - name: Reboot if kernel was updated
    reboot:
      msg: "Kernel updated. Rebooting."
    when: ansible_facts['kernel'] != ansible_facts['kernel']
```

---

### **Best Practices**

1. **Use Descriptive Variable Names**:
   - Example: `http_port` instead of `port`.

2. **Organize Variables**:
   - Use separate variable files for large playbooks.

3. **Leverage Facts**:
   - Use facts to make playbooks adaptable to different environments.

4. **Test Playbooks**:
   - Use the `--check` flag to test playbooks before applying changes.

---

### **Conclusion**
Variables and facts are powerful features in Ansible that enable you to create flexible, dynamic, and reusable playbooks. 
By leveraging system facts and defining variables, you can automate tasks across diverse environments with ease.
Use the examples and best practices above to get started with variables and facts in your Ansible playbooks!
