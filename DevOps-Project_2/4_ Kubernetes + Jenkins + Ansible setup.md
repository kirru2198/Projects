Got it — you just shared a detailed walkthrough of your Kubernetes + Jenkins + Ansible setup and automation process. Here’s a summarized, structured recap of what you did, to make sure I understand fully and to help you with next steps if needed:

---

### Your Setup & Workflow Summary

1. **Machines Setup:**

   * **Jenkins Master (Jenkins M) machine** — manually launched, has Terraform & now Ansible installed.
   * **Kubernetes Master (KM) machine** — created via Terraform.
   * **Kubernetes Slave 1 (KS1) machine** — created via Terraform.
   * **Kubernetes Slave 2 (KS2) machine** — created via Terraform.

2. **Initial Steps:**

   * Connected to all three Kubernetes machines (KM, KS1, KS2).
   * Ran `sudo apt update` on all three.
   * Installed Ansible on Jenkins M machine by creating and executing an install script.

2.1 ** Install Ansible on Jenkins master/Terrafom machine

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
3. **SSH Setup:**

   * Generated SSH key pair (`ssh-keygen`) on Jenkins M.
   * Copied the **public key** (`id_rsa.pub`) from Jenkins M.
   * Pasted that public key into `~/.ssh/authorized_keys` on KM, KS1, and KS2 — enabling passwordless SSH from Jenkins M to all Kubernetes machines.

4. **Ansible Inventory:**

   * Edited Ansible `hosts` file (`/etc/ansible/hosts`) on Jenkins M.
   * Added private IPs for KM, KS1, KS2.
   * Grouped them with labels:

     * `km` for Kubernetes master (will have Java + Kubernetes + Jenkins slave).
     * `ks` for Kubernetes slaves (will have Kubernetes only).

  <img width="578" alt="image" src="https://github.com/user-attachments/assets/b66aa671-0bf3-4cf4-9fd2-fca69ef52953" />


5. **Testing Connection:**

   * Ran `ansible -m ping all` to confirm Ansible can communicate with all machines via SSH.

6. **Playbook Preparation:**

   * Will write a playbook to install:

     * On Jenkins M (localhost): Java + Jenkins.
     * On Kubernetes Master (km): Java + Kubernetes (because it's also Jenkins slave).
     * On Kubernetes Slaves (ks group): Kubernetes only.
   * Installation of Docker will be done manually later after Kubernetes installation.

---

### Next Steps / What You May Want to Do

* Write the **Ansible playbook (`play.yaml`)** with multiple plays targeting different hosts/groups with appropriate tasks for each.
* Prepare **scripts or tasks** for installing Java, Jenkins, Kubernetes on respective machines.
* Execute the playbook via Ansible on Jenkins M.
* Verify successful installations.
* Manually install Docker on Kubernetes nodes (or optionally automate via Ansible later).
* Add Kubernetes master node as Jenkins slave in Jenkins dashboard.
* Continue automation for deployment pipeline using Jenkins and Kubernetes.

---

### Example Snippet of Your Ansible Playbook Structure (simplified):

## 🛠️ Ansible Playbook: Jenkins Master and Kubernetes Setup

This playbook performs the following tasks:

1. Installs Java, Jenkins, and Docker on the Jenkins master (localhost).
2. Installs Java and Kubernetes on the Kubernetes master node (`kmaster`).
3. Installs Kubernetes on all Kubernetes slave nodes (`kslaves`).

**Steps and Notes for Kubernetes Setup Using Ansible Playbook**

---

# 🚀 Jenkins and Kubernetes Setup using Ansible & Shell Scripts

This project uses an Ansible playbook to automate the setup of:

* Jenkins master on localhost
* Kubernetes master on a node (`kmaster`)
* Kubernetes workers on one or more slave nodes (`kslaves`)

---

## 📂 Directory Structure

```
project-root/
├── playbook.yml
├── localhost.sh
├── km.sh
└── ks.sh
```

---

## 🔧 Ansible Playbook

```yaml
---
- name: executing script on jenkins-master
  hosts: localhost
  become: true
  tasks:
    - name: installing java, jenkins, docker on J-M
      script: localhost.sh

- name: executing script on k8s master
  hosts: kmaster
  become: true
  tasks: 
    - name: installing java and k8s
      script: km.sh

- name: executing script on k8s slaves
  hosts: kslaves
  become: true
  tasks: 
    - name: installing k8s
      script: ks.sh
```

---

## 🖥️ `localhost.sh` – Jenkins Master Setup

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" \
  | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install fontconfig openjdk-17-jre -y
sudo apt-get install jenkins -y
```

---

## 🧠 `km.sh` – Kubernetes Master Node Setup

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
sudo apt-get update
sudo apt install apt-transport-https curl -y

# Install containerd
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install containerd.io -y

# Configure containerd
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i -e 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
sudo systemctl restart containerd

# Install Kubernetes
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' \
| sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet

# Networking config
sudo swapoff -a
sudo modprobe br_netfilter
sudo sysctl -w net.ipv4.ip_forward=1
```

---

## 🧱 `ks.sh` – Kubernetes Worker Node Setup

```bash
sudo apt update
sudo apt-get update
sudo apt install apt-transport-https curl -y

# Install containerd
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install containerd.io -y

# Configure containerd
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i -e 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
sudo systemctl restart containerd

# Install Kubernetes
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' \
| sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet

# Networking config
sudo swapoff -a
sudo modprobe br_netfilter
sudo sysctl -w net.ipv4.ip_forward=1
```

---

## ✅ Next Steps

* Run the Ansible playbook:

  ```bash
  ansible-playbook playbook.yml -i inventory
  ```

* Make sure to configure your `inventory` file with proper IPs or hostnames for `localhost`, `kmaster`, and `kslaves`.

---
