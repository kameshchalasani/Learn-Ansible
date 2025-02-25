In Ansible, **conditionals** and **loops** are used to add logic and control flow to your playbooks. Conditionals allow you to execute tasks based on specific conditions, while loops enable you to repeat tasks with different inputs. 
These features make your playbooks more dynamic, flexible, and efficient.

---

### **Conditionals**

#### 1. **What Are Conditionals?**
Conditionals allow you to execute tasks only when certain conditions are met. They are implemented using the `when` keyword.

#### 2. **Basic Syntax**
```yaml
tasks:
  - name: Task name
    module:
      parameter: value
    when: condition
```

#### 3. **Common Use Cases**
- **Check OS Family**:
  ```yaml
  tasks:
    - name: Install Apache on RedHat
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"
  ```

- **Check Variable Value**:
  ```yaml
  tasks:
    - name: Start Apache if enabled
      service:
        name: httpd
        state: started
      when: apache_enabled
  ```

- **Combine Conditions**:
  Use `and`, `or`, and `not` to combine conditions:
  ```yaml
  tasks:
    - name: Install Apache on RedHat or Debian
      package:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat" or ansible_facts['os_family'] == "Debian"
  ```

#### 4. **Using Facts in Conditionals**
Ansible facts can be used in conditionals to make decisions based on system properties:
```yaml
tasks:
  - name: Reboot if kernel was updated
    reboot:
      msg: "Kernel updated. Rebooting."
    when: ansible_facts['kernel'] != ansible_facts['kernel']
```

---

### **Loops**

#### 1. **What Are Loops?**
Loops allow you to repeat a task with different inputs. They are implemented using the `loop` keyword (or `with_` syntax in older versions of Ansible).

#### 2. **Basic Syntax**
```yaml
tasks:
  - name: Task name
    module:
      parameter: "{{ item }}"
    loop:
      - value1
      - value2
```

#### 3. **Common Use Cases**
- **Install Multiple Packages**:
  ```yaml
  tasks:
    - name: Install packages
      yum:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - mariadb
        - php
  ```

- **Create Multiple Users**:
  ```yaml
  tasks:
    - name: Create users
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
      loop:
        - { name: "alice", uid: 1001 }
        - { name: "bob", uid: 1002 }
  ```

- **Iterate Over a Dictionary**:
  ```yaml
  tasks:
    - name: Add users
      user:
        name: "{{ item.key }}"
        uid: "{{ item.value }}"
      loop: "{{ lookup('dict', users) }}"
  vars:
    users:
      alice: 1001
      bob: 1002
  ```

#### 4. **Loop Control**
- **`loop_control`**: Allows you to customize loop behavior.
  ```yaml
  tasks:
    - name: Install packages
      yum:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - mariadb
        - php
      loop_control:
        label: "{{ item }}"
  ```

---

### **Combining Conditionals and Loops**

#### 1. **Conditionals Inside Loops**
You can use conditionals within loops to filter items:
```yaml
tasks:
  - name: Install packages on RedHat
    yum:
      name: "{{ item }}"
      state: present
    loop:
      - httpd
      - mariadb
      - php
    when: ansible_facts['os_family'] == "RedHat"
```

#### 2. **Loops Inside Conditionals**
You can also use loops inside conditionals:
```yaml
tasks:
  - name: Install packages if enabled
    yum:
      name: "{{ item }}"
      state: present
    when: package_enabled
    loop:
      - httpd
      - mariadb
      - php
```

---

### **Advanced Loops**

#### 1. **Nested Loops**
You can nest loops to iterate over multiple lists:
```yaml
tasks:
  - name: Add users to groups
    user:
      name: "{{ item.0 }}"
      groups: "{{ item.1 }}"
    loop: "{{ ['alice', 'bob'] | product(['wheel', 'docker']) | list }}"
```

#### 2. **Using `with_items` (Legacy Syntax)**
In older versions of Ansible, loops were implemented using `with_items`:
```yaml
tasks:
  - name: Install packages
    yum:
      name: "{{ item }}"
      state: present
    with_items:
      - httpd
      - mariadb
      - php
```

---

### **Best Practices**

1. **Use Descriptive Names**:
   - Use meaningful names for tasks and variables to make playbooks easier to understand.

2. **Keep Conditionals Simple**:
   - Avoid complex conditionals; break them into multiple tasks if necessary.

3. **Test Loops**:
   - Test loops with a small set of items to ensure they work as expected.

4. **Use `loop_control` for Clarity**:
   - Use `loop_control` to customize loop output and improve readability.

5. **Avoid Overusing Loops**:
   - Use loops only when necessary to keep playbooks efficient.

---

### **Example Playbook with Conditionals and Loops**

```yaml
---
- name: Configure webservers
  hosts: webservers
  tasks:
    - name: Install Apache on RedHat
      yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"

    - name: Install Apache on Debian
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == "Debian"

    - name: Start Apache service
      service:
        name: "{{ 'httpd' if ansible_facts['os_family'] == 'RedHat' else 'apache2' }}"
        state: started

    - name: Create web directories
      file:
        path: "/var/www/{{ item }}"
        state: directory
      loop:
        - html
        - logs
        - cgi-bin
```

---

### **Conclusion**
Conditionals and loops are powerful tools in Ansible that allow you to implement logic and control flow in your playbooks. 
By using conditionals, you can execute tasks based on specific conditions, while loops enable you to repeat tasks with different inputs. Combining these features allows you to create dynamic, flexible, and efficient automation workflows. 
Use the examples and best practices above to get started with conditionals and loops in your Ansible playbooks!
