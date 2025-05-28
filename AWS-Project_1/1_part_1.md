## **Project 1: High Availability Architecture using EC2, RDS, and Load Balancer**

### **Session Introduction**

* **Audio Check:** "Good morning, everyone. Please confirm if my audio is clear."
* **Agenda:** We will be working on **Project 1** today.
* **Assumption:** You are familiar with **RDS** and **EC2** services.
* **Start Time:** We'll wait until 7:05 for others to join.
* **Preparation:** Please read through the question while we wait. I'll share my screen so you can follow along.

---

### **Problem Statement Overview**

We are tasked with creating a high availability web application that connects to a MySQL database. The steps involve:

1. Launching a web server on EC2.
2. Setting up an RDS MySQL database.
3. Connecting the web server to the database.
4. Making the web application **highly available** using:

   * Auto Scaling
   * Load Balancer

---

### **Step-by-Step Architecture Breakdown**

#### **1. Launch MySQL Database**

* **Service Used:** AWS RDS
* **Reason:** To manage the MySQL database in a scalable and highly available way.

#### **2. Launch the Web Application**

* **Service Used:** AWS EC2
* **Purpose:** Host the application (PHP, Apache/Nginx, etc.)
* **Connection:** The EC2 instance will be configured to connect to the RDS MySQL instance.

#### **3. Make the Application Highly Available**

##### **Step A: Auto Scaling EC2 Instances**

* **Objective:** Automatically adjust the number of EC2 instances based on load.
* **Steps:**

  1. **Install and configure the web server** on an EC2 instance.

  2. **Create an AMI (Amazon Machine Image)** of that EC2 instance.

     * **Why AMI?** It serves as a blueprint containing:

       * OS and architecture
       * Installed software (e.g., Apache, PHP)
       * Configuration files
       * Application code
     * **AMI Internals:**

       * Contains a **snapshot** of the **root EBS volume**, which stores OS files and application code.
       * Snapshots are automatically created when you create an AMI.

  3. **Create a Launch Template**

     * This includes:

       * **AMI ID** (software configuration)
       * **Instance type**, key pair, storage, etc. (hardware configuration)
     * Acts as a reusable definition to launch EC2 instances.

  4. **Create an Auto Scaling Group (ASG)**

     * Uses the launch template to automatically:

       * Add more instances when load increases
       * Remove instances when load decreases
     * This is **horizontal scaling**, not vertical.

##### **Step B: Load Balancer**

* **Service Used:** Application Load Balancer (ALB)
* **Why ALB?**

  * Works at **Layer 7 (HTTP/HTTPS)** of the OSI model.
  * Ideal for web applications.
  * Distributes traffic evenly across multiple EC2 instances in the ASG.

---

### **Final Architecture Components**

* **Amazon RDS (MySQL):** Centralized database
* **Amazon EC2:** Hosts the web server
* **AMI:** Blueprint for replicating web server setup
* **Launch Template:** Configuration to launch EC2 instances
* **Auto Scaling Group:** Maintains desired number of instances
* **Application Load Balancer:** Routes traffic to healthy EC2 instances

---

### **Key Concepts Recap**

| Concept                       | Description                                                                  |
| ----------------------------- | ---------------------------------------------------------------------------- |
| **AMI**                       | Blueprint of an EC2 instance containing OS, installed packages, and app code |
| **Snapshot**                  | Backup of EBS volume used in AMI                                             |
| **Launch Template**           | Defines hardware and software configurations for EC2                         |
| **Auto Scaling Group**        | Adds/removes EC2 instances based on traffic                                  |
| **Application Load Balancer** | Distributes traffic across instances, operates at Layer 7                    |

---

### **Next Steps**

Now that we understand the architecture and purpose of each AWS component, let's proceed to implement the project.

---
