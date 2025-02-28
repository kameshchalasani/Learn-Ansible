## **☁️ Cloud Integration: Managing AWS, Azure, and GCP with Ansible**  

Ansible is a powerful tool for automating cloud infrastructure across **AWS, Azure, and GCP**. It allows you to:  
✅ **Provision** VMs, databases, networks, and storage  
✅ **Deploy** applications seamlessly  
✅ **Manage** security, users, and access policies  
✅ **Automate** backups, scaling, and monitoring  

---

## **1️⃣ AWS Cloud Automation with Ansible**  
### **🔹 Step 1: Install AWS Modules & Dependencies**  
Ensure you have `boto3` and `botocore` installed:  
```bash
pip install boto3 botocore
```

### **🔹 Step 2: Configure AWS Credentials**  
Set up your AWS credentials:  
```bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="us-east-1"
```
OR  
Use `~/.aws/credentials`:  
```ini
[default]
aws_access_key_id = your-access-key
aws_secret_access_key = your-secret-key
region = us-east-1
```

### **🔹 Step 3: Create an EC2 Instance Using Ansible**  
`aws_ec2.yml`:
```yaml
- name: Provision AWS EC2 Instance
  hosts: localhost
  connection: local
  gather_facts: no
  tasks:
    - name: Launch EC2 Instance
      amazon.aws.ec2_instance:
        name: "Ansible-Managed-EC2"
        key_name: my-key
        instance_type: t2.micro
        security_groups: ["default"]
        image_id: ami-0c55b159cbfafe1f0  # Change based on region
        region: us-east-1
        count: 1
      register: ec2_instance

    - name: Show Instance Details
      debug:
        msg: "EC2 Instance {{ ec2_instance.instances[0].id }} created!"
```
Run it:  
```bash
ansible-playbook aws_ec2.yml
```
✅ **Ansible will create an EC2 instance in AWS!** 🎉  

---

## **2️⃣ Azure Cloud Automation with Ansible**  
### **🔹 Step 1: Install Azure Modules**  
```bash
pip install ansible[azure]
```
Ensure **Azure CLI** is installed:  
```bash
az login
```

### **🔹 Step 2: Create an Azure VM Using Ansible**  
`azure_vm.yml`:
```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
    - name: Create VM
      azure.azcollection.azure_rm_virtualmachine:
        resource_group: "MyResourceGroup"
        name: "Ansible-VM"
        vm_size: "Standard_B1s"
        admin_username: "azureuser"
        image:
          offer: "UbuntuServer"
          publisher: "Canonical"
          sku: "18.04-LTS"
          version: "latest"
        os_disk_name: "ansible-os-disk"
      register: azure_vm

    - name: Show VM Info
      debug:
        msg: "Azure VM {{ azure_vm.id }} created successfully!"
```
Run it:  
```bash
ansible-playbook azure_vm.yml
```
✅ **Azure VM is now provisioned!** 🚀  

---

## **3️⃣ GCP Cloud Automation with Ansible**  
### **🔹 Step 1: Install GCP Modules & Authenticate**  
```bash
pip install google-auth google-auth-oauthlib google-auth-httplib2 google-api-python-client
```
Authenticate:  
```bash
gcloud auth application-default login
```

### **🔹 Step 2: Create a GCP Compute Instance Using Ansible**  
`gcp_vm.yml`:
```yaml
- name: Create GCP Instance
  hosts: localhost
  connection: local
  tasks:
    - name: Launch GCP VM
      google.cloud.gcp_compute_instance:
        name: "ansible-gcp-vm"
        machine_type: "n1-standard-1"
        zone: "us-central1-a"
        project: "my-gcp-project"
        auth_kind: "application"
        disks:
          - auto_delete: true
            boot: true
            initialize_params:
              source_image: "projects/debian-cloud/global/images/family/debian-10"
        network_interfaces:
          - network: "default"
      register: gcp_vm

    - name: Show VM Details
      debug:
        msg: "GCP VM {{ gcp_vm.id }} created!"
```
Run it:  
```bash
ansible-playbook gcp_vm.yml
```
✅ **Ansible will now create a GCP VM!** 🚀  

---
