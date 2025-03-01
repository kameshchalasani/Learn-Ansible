# **🔥 Debugging & Testing Ansible Playbooks**  

Debugging and testing Ansible playbooks is essential for ensuring reliable automation. Below are key techniques and best practices for troubleshooting issues in Ansible.

---

## **1️⃣ Enabling Debug Mode**  
Ansible provides built-in debugging options to track errors.

### **🔹 Run Playbook with Verbose Logging**  
```bash
ansible-playbook -i inventory playbook.yml -v  # Basic verbose
ansible-playbook -i inventory playbook.yml -vv # More details
ansible-playbook -i inventory playbook.yml -vvv # Full debug output
```
✅ **Use `-vvv` to get detailed output, including SSH connections.**  

---

## **2️⃣ Using the Debug Module**  
The `debug` module prints variable values for troubleshooting.

### **🔹 Example: Print a Variable**  
```yaml
- name: Debugging Example
  hosts: all
  tasks:
    - name: Display hostname
      debug:
        msg: "This play is running on {{ inventory_hostname }}"
```
🔹 **Run the playbook** and check the output for expected values.  

---

## **3️⃣ Checking Syntax Before Execution**  
Before running a playbook, check for YAML errors.  

```bash
ansible-playbook playbook.yml --syntax-check
```
✅ **Prevents execution if the syntax is incorrect.**  

---

## **4️⃣ Testing a Playbook in Dry Run Mode (`--check`)**  
To simulate execution **without making changes**, use:  

```bash
ansible-playbook playbook.yml --check
```
✅ **Verifies what changes would be made without affecting the system.**  

---

## **5️⃣ Debugging Connection Issues**  
If Ansible can't reach hosts, test the SSH connection:  

```bash
ansible all -m ping -i inventory
```
✅ **Ensures that managed nodes are reachable.**  

If SSH fails, check:  
🔹 **Host connectivity:** `ping <host>`  
🔹 **SSH keys:** `ssh-copy-id user@host`  
🔹 **Ansible inventory:** Verify IPs in `inventory`  

---

## **6️⃣ Debugging Variable Issues**  
List all facts about a host to inspect variables:  

```bash
ansible all -m setup
```
🔹 **Use it to troubleshoot dynamic inventory variables.**  

---

## **7️⃣ Logging Ansible Output to a File**  
Save logs for later analysis:  

```bash
ANSIBLE_LOG_PATH=ansible.log ansible-playbook playbook.yml -vvv
```
✅ **Helps debug long playbooks with detailed logs.**  

---

## **8️⃣ Using Error Handling (`ignore_errors` and `rescue`)**  
Handle failures gracefully with:  

```yaml
- name: Example with Error Handling
  hosts: all
  tasks:
    - name: Attempt to install a package
      yum:
        name: non_existing_package
        state: present
      ignore_errors: yes
```
✅ **Allows playbook execution to continue even if a task fails.**  

---

## **9️⃣ Retrying Failed Tasks (`retries`)**  
For unstable environments, use retries:  

```yaml
- name: Retry task up to 5 times
  hosts: all
  tasks:
    - name: Ensure service is running
      systemd:
        name: nginx
        state: started
      register: result
      retries: 5
      delay: 10
      until: result is success
```
✅ **Retries the task every 10 seconds until successful.**  

---

## **🔟 Using `ansible-lint` for Best Practices**  
Check for errors and best practices with:  

```bash
ansible-lint playbook.yml
```
✅ **Detects syntax issues and suggests improvements.**  

---
