### 🔧 Project Setup: Jenkins Master and Terraform Automation

#### ✅ Step 1: Launch Jenkins Master EC2 Instance

* **Name:** Jenkins Master
* **OS:** Ubuntu (specific version compatible with Ansible, typically Ubuntu 20.04 LTS)
* **Instance Type:** `t2.micro`
* **Key Pair:** Use existing key pair (e.g., `virat`)
* **Security Group:** Default
* **No. of Instances:** 1
* **Action:** Launch the instance manually from AWS console

#### ✅ Step 2: Connect to the Jenkins Master Instance

* Go to AWS EC2 dashboard
* Change instance filter to “Running”
* Connect to the Jenkins Master instance using SSH

#### ✅ Step 3: Install Terraform

* Update the system:

  ```bash
  sudo apt update
  ```
* Follow official [Terraform installation docs](https://developer.hashicorp.com/terraform/downloads) or use:

  ```bash
  wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  sudo apt update && sudo apt install terraform
  ```
* Verify installation:

  ```bash
  terraform version
  ```

---

### 📜 Step 4: Write Terraform Configuration - `main.tf`

```bash
sudo nano main.tf
```

#### Provider Block:

```hcl
provider "aws" {
  region     = "us-east-2"  # Ohio
  access_key = "YOUR_ACCESS_KEY"
  secret_key = "YOUR_SECRET_KEY"
}
```
> for creating access_key and secret_key, you need to click on your user name on the top right cornor of aws console, then go to Security credentials, scroll down the page and click on **Create access key**.

#### Resource Blocks (3 EC2 Instances):

```hcl
resource "aws_instance" "machine2" {
  ami           = "ami-xxxxxxxxxxxxxx"
  instance_type = "t2.medium"
  key_name      = "virat"
  tags = {
    Name = "Kubernetes-Master / Jenkins-Slave"
  }
}

resource "aws_instance" "machine3" {
  ami           = "ami-xxxxxxxxxxxxxx"
  instance_type = "t2.medium"
  key_name      = "virat"
  tags = {
    Name = "Kubernetes-Slave-1"
  }
}

resource "aws_instance" "machine4" {
  ami           = "ami-xxxxxxxxxxxxxx"
  instance_type = "t2.medium"
  key_name      = "virat"
  tags = {
    Name = "Kubernetes-Slave-2"
  }
}
```

> 📌 Note: Use the **same AMI ID** as Jenkins Master for consistency. Retrieve AMI from the AWS EC2 instance details.

---

### 🚀 Step 5: Deploy Using Terraform

* Initialize Terraform:

  ```bash
  terraform init
  ```
* Check plan:

  ```bash
  terraform plan
  ```
* Apply to create instances:

  ```bash
  terraform apply
  ```
* Type `yes` to confirm.

---

### ✅ Step 6: Verify & Connect Instances

* Refresh EC2 dashboard
* You should see 4 instances:

  * 1 Jenkins Master + Kubernetes Master (also acts as Jenkins Slave)
  * 2 Kubernetes Slaves
* Connect to each instance via SSH using the `virat` key

---

### 📝 Notes:

* Jenkins Master is configured manually.
* Kubernetes Master and Slaves are provisioned via Terraform.
* Ensure `t2.medium` instance type for Kubernetes due to resource needs.
* Always use the same key pair for all machines to simplify SSH access.

---
