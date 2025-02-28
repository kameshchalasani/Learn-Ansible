# **🚀 Docker & Kubernetes Automation with Ansible**  

Ansible is a powerful tool for automating **Docker** and **Kubernetes** deployments. It can:  
✅ Install and configure **Docker** and **Kubernetes**  
✅ Deploy and manage **containers**  
✅ Automate **scaling, updates, and monitoring**  
✅ Integrate with **CI/CD pipelines**  

---

## **1️⃣ Installing Docker Using Ansible**  
To install Docker on a remote server, create a playbook:  

### **🔹 Step 1: Install Docker (docker_install.yml)**  
```yaml
- name: Install Docker on Ubuntu
  hosts: docker_hosts
  become: yes
  tasks:
    - name: Install required packages
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present

    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker repository
      apt_repository:
        repo: "deb https://download.docker.com/linux/ubuntu focal stable"
        state: present

    - name: Install Docker
      apt:
        name: docker-ce
        state: present

    - name: Start and enable Docker service
      systemd:
        name: docker
        enabled: yes
        state: started
```
Run it:  
```bash
ansible-playbook -i inventory docker_install.yml
```
✅ **Docker is now installed on remote servers!** 🎉  

---

## **2️⃣ Deploying a Docker Container Using Ansible**  
Now, let's deploy an **NGINX container** using Ansible.  

### **🔹 Step 1: Deploy a Docker Container (docker_deploy.yml)**  
```yaml
- name: Deploy NGINX Container
  hosts: docker_hosts
  become: yes
  tasks:
    - name: Run NGINX container
      docker_container:
        name: nginx_container
        image: nginx:latest
        state: started
        restart_policy: always
        ports:
          - "80:80"
```
Run it:  
```bash
ansible-playbook -i inventory docker_deploy.yml
```
✅ **NGINX is now running in a Docker container!** 🐳  

---

## **3️⃣ Installing Kubernetes (K8s) Using Ansible**  
To set up Kubernetes, we’ll install **kubeadm, kubelet, and kubectl** on a cluster.

### **🔹 Step 1: Install Kubernetes on Master & Worker Nodes (k8s_install.yml)**  
```yaml
- name: Install Kubernetes on Ubuntu
  hosts: k8s_nodes
  become: yes
  tasks:
    - name: Install dependencies
      apt:
        name:
          - apt-transport-https
          - curl
        state: present

    - name: Add Kubernetes GPG key
      apt_key:
        url: https://packages.cloud.google.com/apt/doc/apt-key.gpg
        state: present

    - name: Add Kubernetes repository
      apt_repository:
        repo: "deb http://apt.kubernetes.io/ kubernetes-xenial main"
        state: present

    - name: Install Kubernetes packages
      apt:
        name:
          - kubeadm
          - kubelet
          - kubectl
        state: present
```
Run it:  
```bash
ansible-playbook -i inventory k8s_install.yml
```
✅ **Kubernetes is now installed on all nodes!** 🏗️  

---

## **4️⃣ Deploying a Kubernetes Application with Ansible**  
We’ll now deploy an **NGINX pod** using Ansible.  

### **🔹 Step 1: Create a Kubernetes Deployment Playbook (k8s_deploy.yml)**  
```yaml
- name: Deploy NGINX on Kubernetes
  hosts: k8s_master
  tasks:
    - name: Create an NGINX deployment
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: nginx-deployment
            labels:
              app: nginx
          spec:
            replicas: 2
            selector:
              matchLabels:
                app: nginx
            template:
              metadata:
                labels:
                  app: nginx
              spec:
                containers:
                  - name: nginx
                    image: nginx:latest
                    ports:
                      - containerPort: 80
```
Run it:  
```bash
ansible-playbook -i inventory k8s_deploy.yml
```
✅ **NGINX is now running in a Kubernetes cluster!** 🚀  

---

# **🚀 CI/CD Pipeline with Docker, Kubernetes, and Ansible**  

Integrating **Docker & Kubernetes** into a **CI/CD pipeline** automates the **build, test, and deployment** process.  
✅ **Build** a Docker image  
✅ **Push** it to a container registry (**Docker Hub, AWS ECR, or GCR**)  
✅ **Deploy** the updated image to a **Kubernetes cluster**  
✅ **Automate** everything using **Jenkins or GitHub Actions**  

---

## **1️⃣ CI/CD Workflow Overview**  
1️⃣ **Developer pushes code** to GitHub/GitLab  
2️⃣ **Jenkins/GitHub Actions** triggers a pipeline  
3️⃣ **Builds Docker Image** and pushes it to a **Docker Registry**  
4️⃣ **Deploys the updated app** to **Kubernetes using Ansible**  

---

## **2️⃣ Setting Up a Jenkins Pipeline**  

### **🔹 Step 1: Install Required Jenkins Plugins**  
- **Pipeline**  
- **Docker Pipeline**  
- **Kubernetes CLI**  
- **GitHub/GitLab Integration**  

### **🔹 Step 2: Define Jenkinsfile**  
Create a `Jenkinsfile` in your repository:  

```groovy
pipeline {
    agent any

    environment {
        REGISTRY = "your-dockerhub-username"
        IMAGE_NAME = "your-app"
        TAG = "latest"
        K8S_DEPLOYMENT = "your-k8s-deployment.yml"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $REGISTRY/$IMAGE_NAME:$TAG ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh "docker push $REGISTRY/$IMAGE_NAME:$TAG"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "ansible-playbook -i inventory k8s_deploy.yml"
            }
        }
    }
}
```
✅ **Jenkins will now build, push, and deploy automatically!** 🚀  

---

## **3️⃣ Deploying to Kubernetes Using Ansible**  
Create a playbook `k8s_deploy.yml`:  

```yaml
- name: Deploy to Kubernetes
  hosts: k8s_master
  tasks:
    - name: Update Kubernetes Deployment
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: my-app
          spec:
            replicas: 3
            selector:
              matchLabels:
                app: my-app
            template:
              metadata:
                labels:
                  app: my-app
              spec:
                containers:
                  - name: my-app
                    image: "your-dockerhub-username/your-app:latest"
                    ports:
                      - containerPort: 80
```
Run it:  
```bash
ansible-playbook -i inventory k8s_deploy.yml
```
✅ **Your app is now updated in Kubernetes!** 🎉  

---
