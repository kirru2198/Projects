# DevOps Capstone Project 1

## Overview

In today's session, we will be discussing the **DevOps Capstone Project 1**. The task is based on implementing the **DevOps lifecycle** in a software company, which is a product-based company. The product code is stored in a repository, and we need to deploy this product. Let's walk through the steps involved in solving this problem.

---

### Problem Set

The company has hired us as **Senior DevOps Engineers** to implement the **DevOps lifecycle** as quickly as possible. The product’s code is hosted on a repository, and we need to deploy it efficiently.

1. **Code Repository**: All the code that the company has developed is stored in the repository (the link to this repository is provided in the problem set). https://github.com/hshar/website
2. **Objective**: We need to focus on deploying the product using the DevOps lifecycle.
  
---

### Task Breakdown

#### Task 1: Installing Necessary Software Using Configuration Management Tools

- **Configuration Management Tool**: We are required to use a **configuration management tool** to install the necessary software on the machines. 
- **Tool of Choice**: **Ansible** is the tool we'll be using for this purpose.
  
  **Question**: What is a configuration management tool?
  - **Answer**: A configuration management tool automates the process of managing and configuring servers. Ansible helps in automating software installation and configurations across multiple servers.

---

#### Task 2: Implementing the GitLab (GL) Workflow

- **Objective**: We need to focus on implementing the **GitLab workflow**. This involves:
  - Creating branches for development (e.g., `develop`, `master`).
  - Creating a **Dockerfile** for the application.
  
  **GL Workflow** means managing the repositories and branches, and ensuring the application is properly containerized for deployment.

---

#### Task 3: Automated Code Build on Commit

- **Trigger**: The build should be triggered automatically once a commit is made to either the **master** or **develop** branch. This ensures that any change made is quickly reflected and tested.

  **Tool**: **Jenkins** will be used for this, and it will monitor changes in the repository using **webhooks**.
  
  **Question**: What is a code build tool, and which tool should come to mind?
  - **Answer**: When we talk about **code build**, **Jenkins** is the tool we typically use. Jenkins allows us to automate the build and deployment process.

---

#### Task 4: Deploying Changes to Production

- **Master Branch Commit**: When a commit is made to the **master** branch:
  - **Test** the changes.
  - **Push** the code to the **prod** (production) environment.
  - **Expose** the changes on the server, ensuring the new updates are visible on the browser.

  **Note**: This process should be automatic.

- **Develop Branch Commit**: When a commit is made to the **develop** branch:
  - Only build the code and do not deploy it to the production environment.
  - Do not expose it on the browser. This step is only for testing purposes.

---

#### Task 5: Containerizing the Code Using Docker

- The code should be **containerized** using **Docker**.
- **Dockerfile**: We need to create a **Dockerfile** to define how to build the container image.
- **Docker Task**:
  - Build the Docker image.
  - The image should be deployed on the **production server**.
  - The containerized code should replace the existing `index.html` file located in `/var/www/html` (typically for Apache or Nginx servers).

---

#### Task 6: Jenkins Pipeline Configuration

- We need to define a **Jenkins pipeline** with the following jobs:
  - **Job 1**: **Build** the code.
    - This job should be linked with the **master** branch.
    - The build should be done on a **test server**.
  - **Job 2**: **Test** the code.
    - This job is linked with the **develop** branch.
    - The code should be built and tested on a **test server**.
  - **Job 3**: **Finalize Deployment**.
    - Build the code, test it, and then deploy it on the **production server**.
    - Expose the deployment on the browser for users to see.

---

### Jenkins Job Details

1. **Job 1** (for Master Branch):
   - Build the code.
   - Keep the build on the **test server**.
   
2. **Job 2** (for Develop Branch):
   - Build the code.
   - Keep the build on the **test server**.
   
3. **Job 3** (for Production):
   - Build, test, and deploy the code.
   - Keep the build on the **production server**.
   - Expose the deployed application on the **browser**.

---

### Final Notes

- The **master branch** triggers the build and deployment to production.
- The **develop branch** triggers only the build and test processes.
- **Jenkins** uses webhooks to automatically detect changes in the Git repository and trigger the appropriate jobs.

---

### Conclusion

This is the overview of **Capstone Project 1**. By implementing these steps, you will be able to integrate the **DevOps lifecycle** into a product-based company, automate code deployments, and manage version control effectively.

---

# DevOps Capstone Project 1: Jenkins, Docker, Java, and Ansible Installation on AWS

## Introduction
In this project, we will set up a CI/CD pipeline using Jenkins, Docker, and Java. The tasks will be executed through **Ansible**, a configuration management tool. The setup involves launching three AWS EC2 instances: one **Jenkins Master** machine and two **Jenkins Slave** machines. We will install necessary software like **Jenkins**, **Docker**, and **Java** using **Ansible** to automate the entire setup.

---

## Step-by-Step Execution

### Step 1: **Launch EC2 Instances**

#### 1. **Launch AWS Instances:**
- **Instance 1**: Jenkins Master (`Jenkins M`)
- **Instance 2**: Jenkins Slave 1 (`Jenkins S1`)
- **Instance 3**: Jenkins Slave 2 (`Jenkins S2`)

#### 2. **Operating System:**
- Select **Ubuntu 22.04 LTS** as the operating system (because Ansible works well with Ubuntu 22.04).

#### 3. **Instance Type:**
- Choose **t2.micro** instance type (free tier eligible).

#### 4. **Key Pair:**
- Select or create a key pair (e.g., `my-key-pair.pem`) to access instances via SSH.

#### 5. **Security Group:**
- Assign the **default security group** to allow all traffic, ensuring that the machines can communicate.

#### 6. **Launching Instances:**
- Launch the 3 instances (Jenkins Master + Jenkins Slave 1 + Jenkins Slave 2).

---

### Step 2: **Update All Machines**

After launching the instances, you need to **SSH** into each instance and run the following command to update the system:

```bash
sudo apt update -y
```

- **Jenkins M**: `sudo apt update -y`
- **Jenkins S1**: `sudo apt update -y`
- **Jenkins S2**: `sudo apt update -y`

---

### Step 3: **Install Ansible on Jenkins Master**

Since Ansible will be responsible for managing the installation of other software (Jenkins, Docker, Java), we install Ansible on the **Jenkins Master** machine.

#### 1. **Install Ansible:**
- First, SSH into **Jenkins M** (Master machine).
- Create a script file to install Ansible and run it.

```bash
sudo nano install_ansible.sh
```

Paste the following commands inside the script file to install Ansible:

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

Save and exit the file, then run the script:

```bash
sudo bash install_ansible.sh
```

Check Ansible installation:

```bash
ansible --version
```

---

### Step 4: **Set Up SSH Keys for Ansible**

#### 1. **Generate SSH Keys on Jenkins Master**:
- SSH into **Jenkins M** and generate SSH keys:

```bash
ssh-keygen -t rsa -b 2048
```

- This will generate a **private key** (`id_rsa`) and a **public key** (`id_rsa.pub`).

#### 2. **Copy Public Key to Jenkins Slave Machines**:
- Copy the **public key** (`id_rsa.pub`) to **Jenkins S1** and **Jenkins S2**.

```bash
ssh-copy-id user@<slave-ip>
```

- Repeat the same for both **Jenkins S1** and **Jenkins S2**.

---

### Step 5: **Configure Ansible Inventory and Hosts**

#### 1. **Edit Hosts File**:
- SSH into **Jenkins M** and navigate to the Ansible configuration directory:

```bash
cd /etc/ansible
sudo nano hosts
```

- Add the private IPs of **Jenkins S1** and **Jenkins S2**:

```ini
[jenkins]
<jenkins-s1-ip>
<jenkins-s2-ip>
```

- Save and exit.

#### 2. **Test Connectivity**:
- Test the connectivity between the Jenkins Master and the Jenkins Slaves using Ansible's ping module:

```bash
ansible all -m ping
```

- If the ping is successful for both slaves, you have correctly configured SSH and Ansible.

---

### Step 6: **Install Jenkins, Java, and Docker using Ansible**

#### 1. **Create Ansible Playbook**:
- Create a playbook to install **Jenkins**, **Java**, and **Docker** on the Jenkins Slave machines.

```bash
nano play.yml
```

Inside the playbook file, you can define tasks for installing Jenkins, Java, and Docker. Here’s an example playbook:

```yaml
---
- name: Installing java and jenkins inside my ansible-master machine
  hosts: localhost
  become: yes
  tasks:
    - name: executing script file for installing jenkins and java 
      script: jenkins.sh

- name: Installing java and docker inside my anisble-slaves/jenkins-slaves machine
  hosts: localhost
  become: yes
  tasks:
    - name: executing script file for installing java and docker
      script: slaves.sh
```
#### 2. **Create the file - jenkins.sh**:

```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
```
#### 3. **Create the file - slaves.sh**:

```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
sudo apt install docker.io -y
```
#### 4. **Run the Playbook**:
- Run the playbook using Ansible:

```bash
ansible-playbook play.yml
```

---

### Step 7: **Verify Jenkins Installation on Master**

1. After the playbook has run successfully, verify if Jenkins is installed and running on the **Jenkins S1** and **Jenkins S2** machines.
2. Open a browser and navigate to **http://<jenkins-master-ip>:8080** to access the Jenkins Dashboard.
3. Set up Jenkins, install necessary plugins, and configure the slaves in Jenkins Master.

---

### Summary of Actions Taken:

1. **Launched EC2 Instances**: Created 3 AWS EC2 instances (1 master and 2 slave).
2. **System Update**: Updated all instances to ensure they are using the latest packages.
3. **Installed Ansible**: Installed Ansible on the Jenkins Master machine to handle configuration management.
4. **SSH Configuration**: Configured SSH keys for passwordless authentication between Jenkins Master and Slaves.
5. **Ansible Playbook**: Created and ran an Ansible playbook to install **Jenkins**, **Java**, and **Docker** on Jenkins Slave machines.
6. **Jenkins Verification**: Checked the Jenkins setup and ensured it was running correctly.

---

Now, you can proceed with additional tasks such as setting up Jenkins jobs, creating Docker containers, or configuring the CI/CD pipeline.

---
## **Overview:**

In this process, we are using **Ansible**, a configuration management tool, to automate the installation of software across multiple machines. The goal is to install:
- **Jenkins** and **Java** on the Ansible master machine
- **Java** and **Docker** on the Ansible slave machines

---

## **Steps:**

### 1. **Setting Up the Machines**

We will be working with three virtual machines:
- **Jenkins Master Machine** (Ansible Master)
- **Jenkins Slave Machine 1**
- **Jenkins Slave Machine 2**

We are using **AWS EC2** to launch these machines. 
1. Launch three **Ubuntu 22.04** instances.
2. Name them as:
   - `Jenkins M` (Master)
   - `Jenkins S1` (Slave 1)
   - `Jenkins S2` (Slave 2)

Ensure that the key pair is selected for SSH access and the security group is set to allow all traffic.

### 2. **Update the Machines**

Once the machines are launched, update them with the following command on each machine:

```bash
sudo apt-get update
```

### 3. **Install Ansible on Jenkins Master Machine**

On the **Jenkins Master Machine**, Ansible is installed to manage the installation of software on both the master and slave machines.

1. **Install Ansible on Jenkins Master:**

```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt-get install ansible
```

2. **Verify Ansible Installation:**

```bash
ansible --version
```

### 4. **Configure SSH Access**

For Ansible to manage remote machines, we need to configure SSH access between the master and slave machines.

1. **Generate SSH Keys** on the Jenkins Master:

```bash
ssh-keygen -t rsa -b 2048
```

2. **Copy Public Key to Slave Machines:**

On each slave machine (`Jenkins S1` and `Jenkins S2`), append the master’s public key to the `authorized_keys` file inside the `~/.ssh/` directory.

3. **Test SSH Connection:**

From the Jenkins master, verify that SSH works to both slave machines using:

```bash
ssh <slave_ip>
```

### 5. **Configure the Ansible Inventory File**

Next, configure the **Ansible inventory file** to specify which machines Ansible will manage. 
This file is located at `/etc/ansible/hosts`.

1. Open the file and add the IP addresses of the slave machines:

```bash
sudo nano /etc/ansible/hosts
```

```ini
[slaves]
<slave_ip_1>
<slave_ip_2>
```

2. Save and exit the file.

### 6. **Test Ansible Connection**

Verify that the master machine can communicate with the slave machines using Ansible’s ping module:

```bash
ansible all -m ping
```

If successful, you should see:

```bash
<slave_ip_1> | SUCCESS => {
    "ping": "pong"
}
<slave_ip_2> | SUCCESS => {
    "ping": "pong"
}
```

---

### **Creating the Playbooks**

Next, we need to create **Ansible playbooks** to automate the installation of **Jenkins**, **Java**, and **Docker** on the respective machines.

#### 7. **Create the Ansible Playbook for Jenkins Master**

Create a playbook file `play.yaml`:

```bash
sudo nano play.yaml
```

Add the following content:

```yaml
---
- name: Installing Java and Jenkins on Ansible Master Machine
  hosts: localhost
  become: true
  tasks:
    - name: Install Java
      apt:
        name: openjdk-11-jdk
        state: present
    - name: Add Jenkins Repository
      apt_repository:
        repo: 'deb http://pkg.jenkins.io/debian/ stable main'
        state: present
    - name: Install Jenkins
      apt:
        name: jenkins
        state: present
    - name: Start Jenkins service
      service:
        name: jenkins
        state: started
        enabled: true
```

This playbook will:
1. Install **Java 11** on the master machine.
2. Add the **Jenkins repository** and install **Jenkins**.
3. Start the Jenkins service.

#### 8. **Create the Ansible Playbook for Jenkins Slaves**

Create a playbook for installing **Java** and **Docker** on the slave machines:

```yaml
---
- name: Installing Java and Docker on Jenkins Slave Machines
  hosts: all
  become: true
  tasks:
    - name: Install Java
      apt:
        name: openjdk-17-jdk
        state: present
    - name: Install Docker
      apt:
        name: docker.io
        state: present
    - name: Start Docker service
      service:
        name: docker
        state: started
        enabled: true
```

This playbook will:
1. Install **Java 17** on the slave machines.
2. Install **Docker** on the slave machines.
3. Start the **Docker service**.

#### 9. **Execute the Playbook**

Before executing the playbook, we need to ensure the commands are correct and then run them.

1. **Check Syntax:**

```bash
ansible-playbook --syntax-check play.yaml
```

2. **Run the Playbook:**

```bash
ansible-playbook -i /etc/ansible/hosts play.yaml
```

---

### 10. **Verify Installation**

Finally, verify that the installation was successful:

1. **Check Jenkins Version (on Master):**

```bash
java -version
jenkins --version
```

2. **Check Java Version (on Slave Machines):**

```bash
java -version
```

3. **Check Docker Version (on Slave Machines):**

```bash
docker --version
```

If everything is configured correctly, you should see the versions of **Java**, **Jenkins**, and **Docker** printed.

---

## **Conclusion:**

This process automates the installation of **Jenkins**, **Java**, and **Docker** across multiple machines using **Ansible**. The Ansible master controls the configuration and installation of software on both the master and slave machines, ensuring consistency and efficiency in managing the infrastructure.

### **Key Points:**
- **Ansible** is used as the configuration management tool to install software on multiple machines.
- **SSH** is configured to allow communication between the master and slave machines.
- **Playbooks** are used to automate the installation of software (Jenkins, Java, Docker).
- The **inventory file** is essential to manage and target the correct machines for software installation.

--- 

