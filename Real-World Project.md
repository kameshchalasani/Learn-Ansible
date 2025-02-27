### **🔥 Real-World Project: Deploying a Web App with Ansible + CI/CD**  

In this hands-on project, we'll **automate deployment** of a simple **NGINX-based web application** using **Ansible** and integrate it with **Jenkins or GitHub Actions** for CI/CD.  

---

## **🚀 Project Overview**
### **🔹 Goal**:  
Automate the deployment of a **web application (NGINX + HTML)** on remote servers using **Ansible** and CI/CD.  

### **🔹 Tools Used**:  
✅ **Ansible** – Automates provisioning & deployment  
✅ **Jenkins/GitHub Actions** – Automates CI/CD pipeline  
✅ **NGINX** – Web server for hosting the app  
✅ **AWS EC2 (or Any Linux Server)** – Target server for deployment  

---

## **1️⃣ Setting Up Ansible**
### **Step 1: Install Ansible on Control Node**
On your **Ansible control node** (your local machine or a Jenkins server), install Ansible:  
```bash
sudo apt update && sudo apt install -y ansible
```
Verify installation:  
```bash
ansible --version
```

### **Step 2: Configure Inventory File (`inventory`)**
Define your target servers:  
```ini
[web_servers]
web1 ansible_host=YOUR_SERVER_IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```
✅ Replace `YOUR_SERVER_IP` with your actual server's IP.

---

## **2️⃣ Creating Ansible Playbook**
### **Step 1: Create `playbook.yml`**
This Playbook will:  
🔹 Install NGINX  
🔹 Deploy an HTML web page  
🔹 Start NGINX service  

```yaml
- name: Deploy Web Application
  hosts: web_servers
  become: yes  # Run tasks as sudo
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Deploy Web Page
      copy:
        src: index.html
        dest: /var/www/html/index.html
        mode: '0644'

    - name: Restart NGINX
      systemd:
        name: nginx
        state: restarted
```

---

## **3️⃣ Deploying the Web Page**
### **Step 1: Create `index.html`**
This is the webpage we are deploying:  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to Ansible Deployment!</title>
</head>
<body>
    <h1>Successfully Deployed with Ansible & CI/CD 🚀</h1>
</body>
</html>
```

---

## **4️⃣ Running the Ansible Playbook**
Run the Playbook manually to test:  
```bash
ansible-playbook -i inventory playbook.yml
```
✅ **After execution**, visit `http://YOUR_SERVER_IP` in your browser, and you should see the web page!  

---

## **5️⃣ Automating Deployment with CI/CD**
Now, let’s **automate the Playbook execution** using **Jenkins or GitHub Actions**.

### **📌 Option 1: CI/CD with Jenkins**
#### **Step 1: Create `Jenkinsfile`**
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/yourrepo.git'
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory playbook.yml'
            }
        }
    }
}
```
#### **Step 2: Set Up Jenkins Job**
1. Open **Jenkins → New Item → Pipeline**  
2. Configure **GitHub Webhook** for auto-trigger  
3. Run **Build Now** to deploy  

✅ **Now, every code push triggers automatic deployment!** 🎉  

---

### **📌 Option 2: CI/CD with GitHub Actions**
#### **Step 1: Create `.github/workflows/deploy.yml`**
```yaml
name: Deploy Web App with Ansible

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Install Ansible
        run: sudo apt update && sudo apt install -y ansible

      - name: Run Ansible Playbook
        run: ansible-playbook -i inventory playbook.yml
```
✅ **Now, every `git push` triggers automatic deployment via GitHub Actions!** 🚀  

---

## **6️⃣ Summary**
✅ **Automated Web App Deployment** using Ansible  
✅ **CI/CD Integration** using Jenkins/GitHub Actions  
✅ **NGINX Configured** and running on remote servers  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
### **🔒 Adding Ansible Vault for Secrets Management**  

Now that we have automated web app deployment with Ansible & CI/CD, let’s **secure our sensitive data** (passwords, API keys, SSH credentials) using **Ansible Vault**.

---

## **1️⃣ Why Use Ansible Vault?**  
✅ **Protects sensitive data** (passwords, API keys, SSH keys)  
✅ **Prevents accidental leaks** in Git repositories  
✅ **Securely integrates with CI/CD pipelines**  

---

## **2️⃣ Encrypting Sensitive Variables**  
We’ll store **database credentials, API keys, and SSH keys** in an encrypted file.

### **Step 1: Create an Encrypted File**
Run:  
```bash
ansible-vault create secrets.yml
```
It will **prompt for a password** and open a text editor. Add secrets like this:  
```yaml
db_user: "admin"
db_password: "SuperSecret123!"
api_key: "ABCD-1234-XYZ"
```
Save & exit (`CTRL+X`, `Y`, `Enter`). Now, `secrets.yml` is **encrypted** 🔒.

---

## **3️⃣ Using Encrypted Variables in Playbooks**
Modify your **Playbook (`playbook.yml`)** to use these secrets:  
```yaml
- name: Secure Deployment
  hosts: web_servers
  become: yes
  vars_files:
    - secrets.yml
  tasks:
    - name: Print the API Key (Testing)
      debug:
        msg: "The API Key is {{ api_key }}"
```

### **Running the Playbook**  
When executing the Playbook, Ansible will ask for the Vault password:  
```bash
ansible-playbook -i inventory playbook.yml --ask-vault-pass
```

✅ **Secrets are now securely included in automation!** 🎉  

---

## **4️⃣ Managing Vault Files**
### **Encrypt an Existing File**
```bash
ansible-vault encrypt inventory
```

### **Decrypt a File**
```bash
ansible-vault decrypt inventory
```

### **Edit an Encrypted File**
```bash
ansible-vault edit secrets.yml
```

### **View Encrypted Content**
```bash
ansible-vault view secrets.yml
```

---

## **5️⃣ Automating Vault in CI/CD Pipelines**
To integrate Vault into **Jenkins/GitHub Actions**, store the Vault password securely and use it in pipelines.

### **Using a Vault Password File**  
Create a **`vault_pass.txt`** file and store the Vault password.  
```bash
echo "your-vault-password" > vault_pass.txt
```
Then run Ansible **without prompting for a password**:  
```bash
ansible-playbook -i inventory playbook.yml --vault-password-file=vault_pass.txt
```

✅ **Best Practices**:  
🚫 **Never commit `vault_pass.txt` to GitHub**  
🛠️ **Use CI/CD secrets (Jenkins/GitHub Actions) instead of plaintext files**  

---  
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
### **🐳 Dockerizing the Web App for Kubernetes Deployment**  

Now that we’ve **automated web app deployment using Ansible & CI/CD**, let’s take it **to the next level** by **Dockerizing** our application and deploying it to **Kubernetes**.  

---

## **1️⃣ Why Docker & Kubernetes?**  
✅ **Containerization**: Ensures consistency across environments  
✅ **Scalability**: Easily scale the app with Kubernetes  
✅ **Portability**: Deploy on any cloud or on-premise cluster  
✅ **CI/CD Ready**: Automate deployments with Jenkins/GitHub Actions  

---

## **2️⃣ Dockerizing the Web Application**  
### **Step 1: Create a `Dockerfile`**  
In your project directory, create a `Dockerfile`:  
```Dockerfile
# Use an official NGINX image
FROM nginx:latest  

# Copy the web app to the NGINX default directory
COPY index.html /usr/share/nginx/html/index.html  

# Expose port 80
EXPOSE 80  

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]
```

### **Step 2: Build & Tag the Docker Image**  
Run the following command to **build the image**:  
```bash
docker build -t mywebapp:latest .
```
Run the container to test locally:  
```bash
docker run -d -p 8080:80 mywebapp:latest
```
✅ **Visit** `http://localhost:8080` in a browser to see the web page! 🎉  

### **Step 3: Push Image to Docker Hub**  
Tag & push the image to **Docker Hub (or AWS ECR/GCR)**:  
```bash
docker tag mywebapp:latest your-dockerhub-username/mywebapp:latest
docker push your-dockerhub-username/mywebapp:latest
```

---

## **3️⃣ Kubernetes Deployment**  
### **Step 1: Create Kubernetes Deployment YAML**
Create a file called `deployment.yaml`:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: your-dockerhub-username/mywebapp:latest
          ports:
            - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: webapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### **Step 2: Deploy to Kubernetes**
Run the following commands:  
```bash
kubectl apply -f deployment.yaml
```
Check if the pods are running:  
```bash
kubectl get pods
```
Get the service’s external IP:  
```bash
kubectl get svc webapp-service
```
✅ **Your app is now running in Kubernetes!** 🎉  

---

## **4️⃣ Automating with Ansible**  
Modify your **Ansible Playbook (`playbook.yml`)** to automate the deployment:  
```yaml
- name: Deploy Web App to Kubernetes
  hosts: localhost
  tasks:
    - name: Apply Kubernetes Deployment
      command: kubectl apply -f deployment.yaml
```
Run it:  
```bash
ansible-playbook -i inventory playbook.yml
```
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
### **📊 Adding Monitoring & Logging for Debugging**  

Now that we’ve **Dockerized the Web App** and deployed it to **Kubernetes**, let’s **add monitoring & logging** to track performance, detect failures, and debug issues.  

---

## **1️⃣ Why Monitoring & Logging?**  
✅ **Real-time Metrics** – Track CPU, memory, and response times  
✅ **Centralized Logging** – Aggregate logs from all containers and nodes  
✅ **Alerting & Notifications** – Get notified on failures  
✅ **Better Debugging** – Easily troubleshoot issues  

---

## **2️⃣ Adding Monitoring with Prometheus & Grafana 📈**  
We’ll use **Prometheus** (metrics collection) & **Grafana** (visualization).  

### **Step 1: Deploy Prometheus in Kubernetes**  
Create `prometheus.yaml`:  
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
data:
  prometheus.yml: |
    global:
      scrape_interval: 10s
    scrape_configs:
      - job_name: 'kubernetes-nodes'
        static_configs:
          - targets: ['localhost:9100']

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus:latest
          args:
            - "--config.file=/etc/prometheus/prometheus.yml"
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: config-volume
              mountPath: /etc/prometheus
      volumes:
        - name: config-volume
          configMap:
            name: prometheus-config

---
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
spec:
  selector:
    app: prometheus
  ports:
    - protocol: TCP
      port: 9090
      targetPort: 9090
  type: LoadBalancer
```
Apply the deployment:  
```bash
kubectl apply -f prometheus.yaml
```
✅ **Visit `http://<EXTERNAL_IP>:9090` to access Prometheus UI!** 🎉  

---

### **Step 2: Deploy Grafana for Visualization**  
Create `grafana.yaml`:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
  type: LoadBalancer
```
Apply the deployment:  
```bash
kubectl apply -f grafana.yaml
```
✅ **Visit `http://<EXTERNAL_IP>:3000` and login (`admin/admin`). Connect Prometheus as a data source to visualize metrics!**  

---

## **3️⃣ Adding Logging with EFK Stack (Elasticsearch, Fluentd, Kibana) 📜**  
The **EFK Stack** collects logs from containers & nodes.

### **Step 1: Deploy Elasticsearch**
Create `elasticsearch.yaml`:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: elasticsearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
        - name: elasticsearch
          image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
          env:
            - name: discovery.type
              value: "single-node"
          ports:
            - containerPort: 9200

---
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch-service
spec:
  selector:
    app: elasticsearch
  ports:
    - protocol: TCP
      port: 9200
      targetPort: 9200
  type: ClusterIP
```
Apply the deployment:  
```bash
kubectl apply -f elasticsearch.yaml
```

---

### **Step 2: Deploy Fluentd for Log Collection**
Create `fluentd.yaml`:  
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
        - name: fluentd
          image: fluent/fluentd-kubernetes-daemonset
          env:
            - name: FLUENT_ELASTICSEARCH_HOST
              value: "elasticsearch-service"
          volumeMounts:
            - name: varlog
              mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
            path: /var/log
```
Apply the deployment:  
```bash
kubectl apply -f fluentd.yaml
```

---

### **Step 3: Deploy Kibana for Log Visualization**
Create `kibana.yaml`:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
        - name: kibana
          image: docker.elastic.co/kibana/kibana:7.10.1
          ports:
            - containerPort: 5601
          env:
            - name: ELASTICSEARCH_HOSTS
              value: "http://elasticsearch-service:9200"

---
apiVersion: v1
kind: Service
metadata:
  name: kibana-service
spec:
  selector:
    app: kibana
  ports:
    - protocol: TCP
      port: 5601
      targetPort: 5601
  type: LoadBalancer
```
Apply the deployment:  
```bash
kubectl apply -f kibana.yaml
```
✅ **Visit `http://<EXTERNAL_IP>:5601` to view logs in Kibana!** 🎉  

---

## **4️⃣ Automating with Ansible**  
Modify your **Ansible Playbook (`playbook.yml`)**:  
```yaml
- name: Deploy Monitoring Stack
  hosts: localhost
  tasks:
    - name: Deploy Prometheus
      command: kubectl apply -f prometheus.yaml

    - name: Deploy Grafana
      command: kubectl apply -f grafana.yaml

    - name: Deploy Elasticsearch
      command: kubectl apply -f elasticsearch.yaml

    - name: Deploy Fluentd
      command: kubectl apply -f fluentd.yaml

    - name: Deploy Kibana
      command: kubectl apply -f kibana.yaml
```
Run it:  
```bash
ansible-playbook -i inventory playbook.yml
```

---
### **🔒 Integrating HashiCorp Vault with Jenkins, Ansible & Secure Secrets Management**  

Now, let’s **securely manage secrets** using **HashiCorp Vault** and integrate it with **Jenkins/GitHub Actions & Ansible** for structured deployments.  

---

## **1️⃣ Why Use HashiCorp Vault?**  
✅ **Secure Storage** – Encrypt passwords, SSH keys, and API tokens  
✅ **Dynamic Secrets** – Generate short-lived credentials  
✅ **Access Control** – Restrict secrets based on roles  
✅ **Integration** – Works with Jenkins, Ansible, Kubernetes, and CI/CD  

---

## **2️⃣ Setting Up HashiCorp Vault in Kubernetes**  
We’ll deploy Vault **as a service** inside our Kubernetes cluster.  

### **Step 1: Deploy Vault in Kubernetes**
Create a file called `vault-deployment.yaml`:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vault
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vault
  template:
    metadata:
      labels:
        app: vault
    spec:
      containers:
        - name: vault
          image: hashicorp/vault:latest
          ports:
            - containerPort: 8200
          env:
            - name: VAULT_DEV_ROOT_TOKEN_ID
              value: "myroot"
          args:
            - "server"
          volumeMounts:
            - name: vault-storage
              mountPath: /vault/data
      volumes:
        - name: vault-storage
          emptyDir: {}

---
apiVersion: v1
kind: Service
metadata:
  name: vault-service
spec:
  selector:
    app: vault
  ports:
    - protocol: TCP
      port: 8200
      targetPort: 8200
  type: LoadBalancer
```
Deploy Vault:  
```bash
kubectl apply -f vault-deployment.yaml
```
✅ **Access Vault at `http://<EXTERNAL_IP>:8200`**  
Login with:  
```bash
vault login myroot
```

---

## **3️⃣ Storing Secrets in Vault**
### **Step 1: Enable KV Secrets Engine**
Run the following:  
```bash
vault secrets enable -path=secret kv
```

### **Step 2: Store a Secret**
```bash
vault kv put secret/db username=admin password=SuperSecret123!
```
Retrieve it:  
```bash
vault kv get secret/db
```

---

## **4️⃣ Using Vault in Jenkins for CI/CD 🔄**  
### **Step 1: Install Vault Plugin in Jenkins**
1️⃣ Go to **Manage Jenkins → Plugin Manager**  
2️⃣ Install **Vault Plugin**  

### **Step 2: Configure Vault in Jenkins**
1️⃣ Go to **Manage Jenkins → Configure System**  
2️⃣ Under **Vault Plugin**, set:  
   - **Vault URL**: `http://vault-service:8200`  
   - **Auth Token**: `myroot`  

### **Step 3: Use Vault in a Jenkinsfile**
Modify your `Jenkinsfile` to retrieve secrets dynamically:  
```groovy
pipeline {
    agent any
    environment {
        DB_USER = vault path: 'secret/db', key: 'username'
        DB_PASS = vault path: 'secret/db', key: 'password'
    }
    stages {
        stage('Deploy') {
            steps {
                sh 'echo Deploying with DB User: $DB_USER'
            }
        }
    }
}
```
✅ **Now, Jenkins will securely fetch credentials from Vault!** 🎉  

---

## **5️⃣ Using Vault in an Ansible Role for Secure Deployments**
We’ll create an **Ansible Role** that dynamically fetches secrets.  

### **Step 1: Install Vault CLI in Ansible Control Node**
Run:  
```bash
sudo apt update && sudo apt install vault
```

### **Step 2: Create an Ansible Role to Fetch Secrets**
```bash
ansible-galaxy init roles/vault_secrets
```

Modify `roles/vault_secrets/tasks/main.yml`:  
```yaml
- name: Fetch secrets from Vault
  command: vault kv get -format=json secret/db
  register: vault_output

- name: Set DB variables
  set_fact:
    db_username: "{{ vault_output.stdout | from_json | json_query('data.data.username') }}"
    db_password: "{{ vault_output.stdout | from_json | json_query('data.data.password') }}"
```

Modify `playbook.yml`:  
```yaml
- name: Deploy Secure Application
  hosts: web_servers
  roles:
    - vault_secrets
  tasks:
    - name: Print Vault Secrets
      debug:
        msg: "DB User: {{ db_username }}, DB Pass: {{ db_password }}"
```
Run it:  
```bash
ansible-playbook -i inventory playbook.yml
```
✅ **Now, Ansible securely retrieves secrets from Vault!** 🎉  

---

## **6️⃣ Encrypting SSH Keys & Credentials for Multi-Server Automation**
### **Step 1: Store SSH Private Key in Vault**
```bash
vault kv put secret/ssh private_key=@id_rsa
```

### **Step 2: Retrieve SSH Key in Ansible Playbook**
Modify `playbook.yml`:  
```yaml
- name: Retrieve SSH Key from Vault
  hosts: localhost
  tasks:
    - name: Get SSH Key
      command: vault kv get -field=private_key secret/ssh
      register: ssh_key_output

    - name: Save SSH Key Locally
      copy:
        content: "{{ ssh_key_output.stdout }}"
        dest: "~/.ssh/id_rsa"
        mode: '0600'
```
✅ **Now, SSH keys are encrypted in Vault and dynamically retrieved for automation!** 🔐  

---

## **7️⃣ Automating Everything with Ansible Playbook**
Modify `playbook.yml`:  
```yaml
- name: Secure CI/CD & Ansible with Vault
  hosts: localhost
  tasks:
    - name: Deploy Vault
      command: kubectl apply -f vault-deployment.yaml

    - name: Store DB Secrets
      command: vault kv put secret/db username=admin password=SuperSecret123!

    - name: Store SSH Key
      command: vault kv put secret/ssh private_key=@id_rsa
```
Run it:  
```bash
ansible-playbook -i inventory playbook.yml
```
✅ **Now, everything is automated: Vault setup, secret storage, and secure CI/CD deployment!** 🚀  

---





