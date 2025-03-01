# **🚀 Real-World Scenarios: Practical Ansible Examples for Infrastructure Automation**  

Ansible is widely used for **server provisioning, configuration management, and application deployment**. Below are **real-world scenarios** where Ansible plays a crucial role in automating infrastructure.  

---

## **1️⃣ Automating Server Provisioning (Infrastructure as Code)**
**📌 Scenario:** You need to **set up multiple Linux servers** (Ubuntu) with a predefined configuration.  

### **🔹 Playbook: Provisioning a Web Server**
```yaml
- name: Provision Web Servers
  hosts: webservers
  become: yes
  tasks:
    - name: Install required packages
      apt:
        name: ['nginx', 'git', 'curl']
        state: present
        update_cache: yes

    - name: Ensure Nginx is running and enabled
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Deploy a simple index.html
      copy:
        content: "<h1>Welcome to Ansible Automated Server</h1>"
        dest: /var/www/html/index.html
```
✅ **Automates provisioning of web servers with Nginx and basic configuration.**  

---

## **2️⃣ CI/CD Deployment: Automating Web App Deployment**
**📌 Scenario:** Deploy a web application using **Ansible + Jenkins/GitHub Actions** whenever there is a new commit in the repository.  

### **🔹 Playbook: Deploying a Web App**
```yaml
- name: Deploy Web Application
  hosts: app_servers
  become: yes
  tasks:
    - name: Clone Git Repository
      git:
        repo: "https://github.com/example/webapp.git"
        dest: /var/www/webapp
        version: master
        force: yes

    - name: Restart Web Service
      service:
        name: apache2
        state: restarted
```
✅ **Use this in a Jenkins Pipeline for automated deployments!**  

---

## **3️⃣ Ansible + Docker: Automating Containerized Environments**
**📌 Scenario:** Set up a **Docker-based environment** with multiple containers.  

### **🔹 Playbook: Deploying an App with Docker**
```yaml
- name: Deploy App in Docker Container
  hosts: docker_hosts
  become: yes
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes

    - name: Pull and Run Nginx Container
      docker_container:
        name: nginx_container
        image: nginx:latest
        state: started
        ports:
          - "80:80"
```
✅ **Automates Docker container deployment on remote servers.**  

---

## **4️⃣ Cloud Automation: Managing AWS Infrastructure**
**📌 Scenario:** Use Ansible to create and configure **AWS EC2 instances**.  

### **🔹 Playbook: Launch EC2 Instances on AWS**
```yaml
- name: Create AWS EC2 Instance
  hosts: localhost
  tasks:
    - name: Launch EC2 instance
      amazon.aws.ec2_instance:
        name: Ansible-Server
        key_name: my-key
        instance_type: t2.micro
        security_group: default
        image_id: ami-0abcdef1234567890
        region: us-east-1
        count: 1
```
✅ **Combines Ansible with AWS to automate cloud infrastructure.**  

---

## **5️⃣ Ansible Vault: Secure Credential Management**
**📌 Scenario:** Store **database passwords, API keys, and SSH credentials** securely.  

### **🔹 Encrypt a Secret File**
```bash
ansible-vault encrypt secrets.yml
```

### **🔹 Using Vault in a Playbook**
```yaml
- name: Secure DB Credentials
  hosts: db_servers
  become: yes
  vars_files:
    - secrets.yml
  tasks:
    - name: Configure Database
      mysql_user:
        name: "{{ db_user }}"
        password: "{{ db_password }}"
        host: localhost
        priv: "db_name.*:ALL"
```
✅ **Ensures sensitive data is encrypted in your automation workflows.**  

---
