# 🚀 **Ansible Galaxy: Leveraging Pre-Built Roles & Sharing Your Own**  

Ansible Galaxy is a powerful community-driven repository where you can **discover, download, and share Ansible roles**. It enables DevOps teams to **reuse pre-built automation scripts** rather than starting from scratch, saving time and effort.

---

## **1️⃣ What is Ansible Galaxy?**
Ansible Galaxy is:
✅ A **hub for sharing Ansible roles** (like a package manager for roles).  
✅ A place to **download and import roles** from the community.  
✅ A way to **create and share your own roles** with best practices.  
✅ Useful for managing **complex configurations** with reusable components.  

🔗 **Official Galaxy URL**: [https://galaxy.ansible.com/](https://galaxy.ansible.com/)

---

## **2️⃣ Why Use Ansible Galaxy?**
🔹 **Saves Time** – No need to write everything from scratch.  
🔹 **Best Practices** – Use well-tested, community-approved roles.  
🔹 **Consistency** – Standardize configurations across teams and projects.  
🔹 **Easier Maintenance** – Update roles centrally instead of modifying multiple Playbooks.  

---

## **3️⃣ Using Pre-Built Roles from Ansible Galaxy**
### 📥 **A. Installing a Role from Ansible Galaxy**
To install a role, use the `ansible-galaxy install` command:
```bash
ansible-galaxy install geerlingguy.nginx
```
🔍 This downloads and installs the `nginx` role from **Jeff Geerling's** repository.

👉 Installed roles are typically stored in `~/.ansible/roles/` or a custom path defined in `ansible.cfg`.

### 📄 **B. Using a Downloaded Role in a Playbook**
```yaml
---
- name: Deploy Nginx
  hosts: webservers
  roles:
    - geerlingguy.nginx
```
This Playbook will configure `nginx` on the target servers using the downloaded role.

---

## **4️⃣ Managing Multiple Roles with `requirements.yml`**
Instead of installing roles one by one, you can define a `requirements.yml` file:

```yaml
---
roles:
  - name: geerlingguy.nginx
  - name: geerlingguy.mysql
    version: "3.3.0"
  - name: my_custom_role
    src: https://github.com/myrepo/ansible-role-custom.git
    scm: git
```
Then install all roles at once:
```bash
ansible-galaxy install -r requirements.yml
```

---

## **5️⃣ Creating and Sharing Your Own Role**
### 🛠 **A. Generating a New Role**
Use `ansible-galaxy init` to scaffold a new role:
```bash
ansible-galaxy init my_custom_role
```
This creates a **standard directory structure**:

```
my_custom_role/
│── defaults/
│── files/
│── handlers/
│── meta/
│── tasks/
│── templates/
│── vars/
└── README.md
```
Each folder has a specific purpose:
- **tasks/** → Main logic for the role.  
- **handlers/** → Tasks triggered on changes (e.g., restart a service).  
- **templates/** → Jinja2 templates for configuration files.  
- **vars/** and **defaults/** → Store variables (defaults are lower priority).  

### 📤 **B. Uploading Your Role to Ansible Galaxy**
To share your role:
1️⃣ Log in to [https://galaxy.ansible.com](https://galaxy.ansible.com).  
2️⃣ **Link your GitHub repository**.  
3️⃣ Use the `ansible-galaxy` CLI to import your role:
   ```bash
   ansible-galaxy role import your-github-username my_custom_role
   ```
4️⃣ Now, others can install your role using:
   ```bash
   ansible-galaxy install your-github-username.my_custom_role
   ```

---

## **6️⃣ Advanced Ansible Galaxy Features**
🔸 **Role Dependencies:** Define dependent roles inside `meta/main.yml`.  
🔸 **Role Versioning:** Specify versions in `requirements.yml`.  
🔸 **Private Role Hosting:** Use a private Git repository instead of Galaxy.  
🔸 **CI/CD Integration:** Automate role testing with Molecule and GitHub Actions.  

---

## **7️⃣ Best Practices for Ansible Galaxy Roles**
✅ **Keep roles modular & reusable** – Avoid hardcoding environment-specific values.  
✅ **Use default variables (`defaults/main.yml`)** – Allow users to override configurations.  
✅ **Follow role naming conventions** – Use `yourname.rolename` format.  
✅ **Document usage properly** – Provide a clear `README.md` with examples.  
✅ **Test roles with Molecule** – Ensure roles work across different OS versions.  

---
