In Ansible, **modules** are the building blocks used to perform specific tasks on managed nodes. 
They are reusable, standalone scripts that Ansible executes on remote hosts. 
Modules can be **core** (built-in), **custom** (user-created), or **community** (contributed by the Ansible community). 
Let’s explore each type and how to use them.

---

### **Types of Modules**

#### 1. **Core Modules**
- Core modules are **built-in** and maintained by the Ansible team.
- They are included with Ansible by default and cover a wide range of tasks.
- Examples:
  - **`yum`**: Manage packages on RedHat-based systems.
  - **`apt`**: Manage packages on Debian-based systems.
  - **`copy`**: Copy files to remote hosts.
  - **`service`**: Manage services.
  - **`file`**: Manage files and directories.

#### 2. **Custom Modules**
- Custom modules are **user-created** modules tailored to specific needs.
- They can be written in any programming language (Python is recommended).
- Custom modules are stored in a directory specified by the `library` parameter in the Ansible configuration or playbook.

#### 3. **Community Modules**
- Community modules are **contributed by the Ansible community** and are not part of the core Ansible distribution.
- They are available in the **Ansible Galaxy** repository.
- Examples:
  - **`docker_container`**: Manage Docker containers.
  - **`aws_ec2`**: Manage AWS EC2 instances.
  - **`slack`**: Send notifications to Slack.

---

### **Using Modules in Playbooks**

#### 1. **Core Module Example**
Here’s an example of using the `yum` core module to install Apache:
```yaml
tasks:
  - name: Install Apache
    yum:
      name: httpd
      state: present
```

#### 2. **Community Module Example**
To use a community module, you may need to install it first from Ansible Galaxy:
```bash
ansible-galaxy collection install community.docker
```
Then, use it in a playbook:
```yaml
tasks:
  - name: Start a Docker container
    community.docker.docker_container:
      name: my_container
      image: nginx
      state: started
```

#### 3. **Custom Module Example**
If you have a custom module named `my_custom_module.py`, you can use it like this:
```yaml
tasks:
  - name: Run custom module
    my_custom_module:
      param1: value1
      param2: value2
```

---

### **Writing Custom Modules**

#### 1. **Basic Structure**
Custom modules are typically written in Python. Here’s a simple example:
```python
#!/usr/bin/python

from ansible.module_utils.basic import AnsibleModule

def main():
    module = AnsibleModule(
        argument_spec=dict(
            message=dict(type='str', required=True),
        )
    )

    message = module.params['message']
    result = dict(
        changed=False,
        original_message=message,
        message="Hello, " + message,
    )

    module.exit_json(**result)

if __name__ == '__main__':
    main()
```

#### 2. **Using the Custom Module**
Save the module as `my_custom_module.py` and place it in the `library` directory. Then, use it in a playbook:
```yaml
tasks:
  - name: Run custom module
    my_custom_module:
      message: "World"
```

---

### **Installing Community Modules**

Community modules are distributed as **collections** via Ansible Galaxy. To install a collection:
```bash
ansible-galaxy collection install community.general
```

Once installed, you can use the modules in your playbooks:
```yaml
tasks:
  - name: Send a Slack notification
    community.general.slack:
      token: "xoxb-1234567890"
      msg: "Hello from Ansible!"
      channel: "#general"
```

---

### **Common Modules and Their Uses**

#### 1. **Package Management**
- **`yum`**: Install/remove packages on RedHat-based systems.
- **`apt`**: Install/remove packages on Debian-based systems.

#### 2. **File Management**
- **`copy`**: Copy files to remote hosts.
- **`file`**: Manage files and directories (e.g., create, delete, set permissions).

#### 3. **Service Management**
- **`service`**: Start, stop, or restart services.

#### 4. **Cloud Modules**
- **`aws_ec2`**: Manage AWS EC2 instances.
- **`azure_rm_virtualmachine`**: Manage Azure virtual machines.

#### 5. **Container Management**
- **`docker_container`**: Manage Docker containers.
- **`k8s`**: Manage Kubernetes resources.

#### 6. **Notification Modules**
- **`slack`**: Send notifications to Slack.
- **`mail`**: Send emails.

---

### **Best Practices for Using Modules**

1. **Use Core Modules When Possible**:
   - Core modules are well-tested and maintained by the Ansible team.

2. **Leverage Community Modules**:
   - Use community modules for specialized tasks (e.g., cloud, containers).

3. **Write Custom Modules for Unique Needs**:
   - Create custom modules for tasks not covered by core or community modules.

4. **Test Modules**:
   - Use the `--check` flag to test modules in dry-run mode.

5. **Document Modules**:
   - Add comments and documentation to custom modules for clarity.

---

### **Example Playbook Using Multiple Modules**

```yaml
---
- name: Configure webserver
  hosts: webservers
  remote_user: admin
  become: yes
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present

    - name: Copy index.html
      copy:
        src: files/index.html
        dest: /var/www/html/index.html

    - name: Start Apache service
      service:
        name: httpd
        state: started

    - name: Send Slack notification
      community.general.slack:
        token: "xoxb-1234567890"
        msg: "Webserver configuration complete!"
        channel: "#alerts"
```

---

### **Conclusion**
Modules are the heart of Ansible automation, enabling you to perform a wide range of tasks on managed nodes. 
Whether you use core, custom, or community modules, they provide the flexibility and power needed to automate your infrastructure.
By following best practices and leveraging the right modules, you can create efficient and reusable playbooks for your automation needs.
