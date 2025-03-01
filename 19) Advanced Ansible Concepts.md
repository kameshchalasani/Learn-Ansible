# **🚀 Advanced Ansible Concepts: Custom Modules, Plugins & Performance Tuning**  

### 🔹 **Why Go Advanced?**  
Mastering **custom modules, plugins, and performance tuning** allows you to:  
✅ Extend Ansible's functionality beyond built-in modules.  
✅ Optimize performance for large-scale infrastructure.  
✅ Automate complex workflows efficiently.  

---

## **1️⃣ Custom Ansible Modules**  
Ansible allows you to create **custom modules** in Python or any executable script.

### **🔹 When to Use Custom Modules?**  
- When Ansible’s built-in modules **don’t support your use case**.  
- When you need to **integrate with custom APIs or internal tools**.  
- When performance is a concern, and you need **more control**.  

### **🔹 Writing a Simple Python Module**  
```python
#!/usr/bin/python

from ansible.module_utils.basic import AnsibleModule

def main():
    module = AnsibleModule(argument_spec={"name": {"required": True, "type": "str"}})
    name = module.params["name"]
    module.exit_json(changed=False, message=f"Hello, {name}!")

if __name__ == "__main__":
    main()
```
✅ **Place this in `library/` inside your playbook directory, and run it like any other module!**  

```yaml
- name: Custom Module Example
  hosts: localhost
  tasks:
    - name: Say Hello
      my_custom_module:
        name: "Ansible User"
      register: result

    - debug:
        msg: "{{ result.message }}"
```
---

## **2️⃣ Custom Ansible Plugins**  
Ansible supports **multiple plugin types**, including:  
- **Callback plugins** → Customize output/logging.  
- **Lookup plugins** → Fetch data from external sources.  
- **Filter plugins** → Process Jinja2 templates dynamically.  

### **🔹 Example: Custom Filter Plugin**  
```python
# Save in `filter_plugins/custom_filters.py`
def uppercase(text):
    return text.upper()

class FilterModule(object):
    def filters(self):
        return {"uppercase": uppercase}
```
✅ **Use it in a playbook:**  
```yaml
- name: Use Custom Filter
  hosts: localhost
  tasks:
    - debug:
        msg: "{{ 'hello ansible' | uppercase }}"
```
---

## **3️⃣ Performance Tuning for Large-Scale Deployments**  
### **🔹 Enable Pipelining**  
Reduce SSH overhead for faster execution.  
```ini
# /etc/ansible/ansible.cfg
[ssh_connection]
pipelining = True
```

### **🔹 Use Forks to Run Tasks in Parallel**  
Increase concurrent task execution.  
```ini
[defaults]
forks = 50  # Default is 5, increase based on infrastructure
```

### **🔹 Fact Caching for Speed Improvement**  
Avoid gathering facts repeatedly.  
```ini
[defaults]
gathering = smart
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_facts
```

---

## **4️⃣ Asynchronous & Parallel Execution**  
Speed up execution using `async` and `poll`:  
```yaml
- name: Run task asynchronously
  hosts: all
  tasks:
    - name: Install Package
      apt:
        name: nginx
        state: present
      async: 300  # Maximum execution time in seconds
      poll: 5     # Check every 5 seconds
```
✅ **Improves efficiency in large environments.**  

---

## **5️⃣ Working with Ansible Content Collections**  
- Ansible **Collections** allow packaging modules, plugins, and roles together.  
- Example: `ansible-galaxy collection install community.general`  

---
