# DevOps CI/CD Pipeline Implementation Guide

## 📋 Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Project Setup](#project-setup)
- [Step-by-Step Implementation](#step-by-step-implementation)
- [Jenkins Configuration](#jenkins-configuration)
- [Docker Implementation](#docker-implementation)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

## 🎯 Overview

This project demonstrates the implementation of a complete DevOps CI/CD pipeline using:
- **AWS EC2** instances for infrastructure
- **Ansible** for configuration management
- **Jenkins** for continuous integration/deployment
- **Docker** for containerization
- **Git/GitHub** for version control

### 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Jenkins       │    │   Test Server   │    │   Prod Server   │
│   Master        │───▶│   (Slave 1)     │    │   (Slave 2)     │
│                 │    │                 │    │                 │
│ • Jenkins       │    │ • Java          │    │ • Java          │
│ • Ansible       │    │ • Docker        │    │ • Docker        │
│ • Java          │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 🎯 Project Objectives

1. **Automated Infrastructure**: Use Ansible to install and configure software
2. **Git Workflow**: Implement proper branching strategy (master/develop)
3. **Automated Builds**: Trigger builds on code commits using webhooks
4. **Environment Management**: Separate test and production environments
5. **Containerization**: Deploy applications using Docker containers

## ✅ Prerequisites

- AWS Account with EC2 access
- Basic understanding of Linux commands
- GitHub account
- SSH key pair for EC2 instances

## 🚀 Project Setup

### Step 1: Launch AWS EC2 Instances

Create three Ubuntu 22.04 LTS instances:

| Instance | Purpose | Type | Security Group |
|----------|---------|------|----------------|
| Jenkins Master | CI/CD Controller | t2.micro | Default (All traffic) |
| Test Server (Slave 1) | Development builds | t2.micro | Default (All traffic) |
| Prod Server (Slave 2) | Production deployment | t2.micro | Default (All traffic) |

**Launch Commands:**
```bash
# Select Ubuntu 22.04 LTS
# Instance type: t2.micro
# Key pair: Create or select existing
# Security group: Default (allow all traffic)
```

### Step 2: Initial Server Setup

Connect to each instance and update:

```bash
# For all three instances
sudo apt update -y
```

## 🔧 Step-by-Step Implementation

### Phase 1: Ansible Configuration

#### 1.1 Install Ansible on Jenkins Master

```bash
# SSH into Jenkins Master
ssh -i your-key.pem ubuntu@jenkins-master-ip

# Create installation script
sudo nano install_ansible.sh
```

**install_ansible.sh content:**
```bash
#!/bin/bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

```bash
# Execute the script
sudo bash install_ansible.sh

# Verify installation
ansible --version
```

#### 1.2 Configure SSH Key Authentication

**On Jenkins Master:**
```bash
# Generate SSH key pair
ssh-keygen -t rsa
# Press Enter for all prompts

# Display public key
cat ~/.ssh/id_rsa.pub
```

**On Both Slave Machines:**
```bash
# Create .ssh directory if it doesn't exist
mkdir -p ~/.ssh

# Add master's public key to authorized_keys
nano ~/.ssh/authorized_keys
# Paste the public key from master
```

**Test SSH Connection:**
```bash
# From Jenkins Master, test connection to slaves
ssh ubuntu@<slave-1-private-ip>
ssh ubuntu@<slave-2-private-ip>
```

#### 1.3 Configure Ansible Inventory

```bash
# Edit Ansible hosts file
sudo nano /etc/ansible/hosts

# Add slave private IPs
<jenkins-slave-1-private-ip>
<jenkins-slave-2-private-ip>

# Test connectivity
ansible all -m ping
```

Expected output:
```
<slave-ip-1> | SUCCESS => {
    "ping": "pong"
}
<slave-ip-2> | SUCCESS => {
    "ping": "pong"
}
```

### Phase 2: Software Installation with Ansible

#### 2.1 Create Ansible Playbook

```bash
nano play.yml
```

**play.yml content:**
```yaml
---
- name: Installing java and jenkins on ansible-master machine
  hosts: localhost
  become: yes
  tasks:
    - name: executing script file for installing jenkins and java
      script: jenkins.sh

- name: Installing java and docker on ansible-slaves/jenkins-slaves machine
  hosts: all
  become: yes
  tasks:
    - name: executing script file for installing java and docker
      script: slaves.sh
```

#### 2.2 Create Installation Scripts

**jenkins.sh (for Master):**
```bash
#!/bin/bash
# Update system
sudo apt update

# Install Java 21
sudo apt install openjdk-21-jdk -y

# Add Jenkins repository key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add Jenkins repository
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update and install Jenkins
sudo apt-get update
sudo apt-get install fontconfig openjdk-17-jre -y
sudo apt-get install jenkins -y
```

**slaves.sh (for Slaves):**
```bash
#!/bin/bash
# Update system
sudo apt update

# Install Java 17
sudo apt install openjdk-17-jdk -y

# Install Docker
sudo apt install docker.io -y

# Add current user to docker group
sudo usermod -aG docker $USER
```

#### 2.3 Execute Ansible Playbook

```bash
# Check syntax
ansible-playbook --syntax-check play.yml

# Run playbook
ansible-playbook -i /etc/ansible/hosts play.yml
```

#### 2.4 Verify Installation

```bash
# On Master - Check Jenkins and Java
java -version
jenkins --version

# On Slaves - Check Java and Docker
java -version
docker --version
```

### Phase 3: Jenkins Configuration

#### 3.1 Access Jenkins Dashboard

1. Get Jenkins Master public IP
2. Open browser: `http://<jenkins-master-public-ip>:8080`
3. Get admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

#### 3.2 Initial Jenkins Setup

1. Enter admin password
2. Click "Install suggested plugins"
3. Create admin user account
4. Complete setup

#### 3.3 Configure Jenkins Nodes

**Add Test Node (Slave 1):**
1. Go to `Manage Jenkins` → `Manage Nodes and Clouds`
2. Click `New Node`
3. Node name: `test`
4. Type: `Permanent Agent`
5. Configuration:
   - Remote root directory: `/home/ubuntu/jenkins`
   - Labels: `test`
   - Launch method: `Launch agents via SSH`
   - Host: `<slave-1-private-ip>`
   - Credentials: Add SSH username with private key
   - Host Key Verification Strategy: `Non verifying Verification Strategy`

**Add Production Node (Slave 2):**
1. Repeat above steps with:
   - Node name: `prod`
   - Labels: `prod`
   - Host: `<slave-2-private-ip>`

### Phase 4: Docker Implementation

#### 4.1 Fork and Prepare Repository

1. Fork the repository: https://github.com/hshar/website
2. Create a Dockerfile in the repository root:

```dockerfile
# Pull the base image
FROM ubuntu

# Update the package list
RUN apt-get update

# Install Apache web server
RUN apt-get install apache2 -y

# Copy the index.html file from the repository into the container
ADD index.html /var/www/html/

# Keep the container running
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

#### 4.2 Configure GitHub Webhook

1. Go to your forked repository settings
2. Navigate to `Webhooks`
3. Click `Add webhook`
4. Payload URL: `http://<jenkins-master-public-ip>:8080/github-webhook/`
5. Content type: `application/json`
6. Events: `Just the push event`
7. Active: ✅

### Phase 5: Jenkins Jobs Configuration

#### 5.1 Job 1 - Test Build (Master Branch)

```bash
# Job Configuration:
- Name: job1
- Type: Freestyle project
- Restrict where project can run: test
- Source Code Management: Git
  - Repository URL: <your-forked-repo-url>
  - Branch: */master
- Build Triggers: GitHub hook trigger for GITScm polling
```

#### 5.2 Job 2 - Development Build (Develop Branch)

```bash
# Job Configuration:
- Name: job2
- Type: Freestyle project
- Restrict where project can run: test
- Source Code Management: Git
  - Repository URL: <your-forked-repo-url>
  - Branch: */develop
- Build Triggers: GitHub hook trigger for GITScm polling
```

#### 5.3 Job 3 - Production Deployment

```bash
# Job Configuration:
- Name: job3
- Type: Freestyle project
- Restrict where project can run: prod
- Source Code Management: Git
  - Repository URL: <your-forked-repo-url>
  - Branch: */master
- Build Triggers: GitHub hook trigger for GITScm polling
```

**Build Step - Execute Shell:**
```bash
# Remove existing container if exists
sudo docker rm -f sc1 || true

# Build Docker image
sudo docker build -t new_img .

# Run Docker container
sudo docker run -it -d -p 93:80 --name sc1 new_img
```

## 🐛 Troubleshooting

### Common Issues and Solutions

#### 1. SSH Connection Issues
```bash
# Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub

# Verify SSH connectivity
ssh -v ubuntu@<slave-ip>
```

#### 2. Docker Permission Issues
```bash
# Add user to docker group
sudo usermod -aG docker ubuntu
# Logout and login again
```

#### 3. Port Binding Conflicts
```bash
# Remove existing containers
sudo docker rm -f $(sudo docker ps -aq)

# Check port usage
sudo netstat -tlnp | grep :93
```

#### 4. Jenkins Node Connection Issues
- Verify private IP addresses
- Check security group settings
- Ensure SSH keys are correctly configured

## 🎯 Testing the Pipeline

### 1. Test Development Workflow
1. Create a `develop` branch in your repository
2. Make changes to `index.html`
3. Commit and push to `develop` branch
4. Verify Job 2 builds automatically

### 2. Test Production Deployment
1. Make changes to `index.html` in `master` branch
2. Commit and push changes
3. Verify Job 3 builds and deploys automatically
4. Access application: `http://<prod-server-public-ip>:93`

## 📝 Best Practices

### Security
- Use IAM roles for EC2 instances instead of access keys
- Implement proper security groups with minimal required access
- Regularly update SSH keys and rotate credentials

### Jenkins
- Use Jenkins pipelines (Jenkinsfile) for complex workflows
- Implement proper backup strategies for Jenkins configuration
- Use Jenkins agents efficiently to distribute load

### Docker
- Use multi-stage builds for smaller images
- Implement health checks in containers
- Use specific image versions instead of `latest`

### Git Workflow
- Protect master branch with branch protection rules
- Use pull requests for code reviews
- Implement proper commit message conventions

## 🔗 Useful Commands Reference

### Ansible Commands
```bash
# Test connectivity
ansible all -m ping

# Run ad-hoc commands
ansible all -m shell -a "uptime"

# Check playbook syntax
ansible-playbook --syntax-check playbook.yml
```

### Docker Commands
```bash
# List running containers
sudo docker ps

# Remove all containers
sudo docker rm -f $(sudo docker ps -aq)

# Remove all images
sudo docker rmi $(sudo docker images -q)

# View container logs
sudo docker logs <container-name>
```

### Jenkins CLI Commands
```bash
# Restart Jenkins
sudo systemctl restart jenkins

# Check Jenkins status
sudo systemctl status jenkins

# View Jenkins logs
sudo tail -f /var/log/jenkins/jenkins.log
```

---

## 📚 Additional Resources

- [Jenkins Official Documentation](https://www.jenkins.io/doc/)
- [Ansible Documentation](https://docs.ansible.com/)
- [Docker Documentation](https://docs.docker.com/)
- [AWS EC2 User Guide](https://docs.aws.amazon.com/ec2/)

---

**Note**: This guide provides a complete DevOps CI/CD pipeline setup. Customize the configurations based on your specific requirements and security policies.
