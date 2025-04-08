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

Let me know if you have any questions or if something is unclear!
