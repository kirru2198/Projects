# ✅ **Project Deployment – Part 2: AMI, Launch Template & Auto Scaling**

---

## 🎯 **Objective**

Create an AMI (Amazon Machine Image) from a configured EC2 instance, then use it to set up:

* A **Launch Template**
* An **Auto Scaling Group (ASG)** with Load Balancer

---

## ✅ **Step 1: Create an AMI (Amazon Machine Image)**

### 🔹 What is an AMI?

An AMI is a snapshot/template of your EC2 instance. It contains:

* OS settings
* Installed packages
* Your deployed website
* Configuration files

### 🔹 Steps to create an AMI:

1. Go to **EC2 Dashboard** → **Instances**
2. Select your instance (the one with the website and DB configured)
3. Click **Actions** → **Image and templates** → **Create image**
4. Enter a name, e.g., `project-image`
5. Click **Create image**

### 🕐 Wait:

* Go to **AMIs** (left menu)
* Status: Should change from **pending → available**
* Also check **Snapshots** tab: You’ll see a snapshot created for this image

> ❗ This AMI will be used to launch identical EC2 instances with the same setup

---

## ✅ **Step 2: Create a Launch Template**

### 🔹 Steps:

1. In EC2 left menu → Click **Launch Templates**
2. Click **Create launch template**
3. Fill details:

   * **Name**: e.g., `pros-template`
   * **AMI**: Under “My AMIs”, select the AMI you just created
   * **Instance type**: e.g., `t2.micro` (free tier)
   * **Key pair**: Choose existing key pair (e.g., `wick-northvirginia`)
   * **Security group**: Select your project’s SG (e.g., `pros-project-sg`)
4. Scroll down and click **Create launch template**

---

## ✅ **Step 3: Create Auto Scaling Group (ASG)**

### 🔹 Steps:

1. Go to **Auto Scaling Groups** → Click **Create Auto Scaling group**
2. Fill details:

   * **Name**: e.g., `asg-pros`
   * **Launch template**: Select the one created earlier (`pros-template`)
3. Click **Next**

---

## ✅ **Step 4: Network Configuration**

1. Choose your **VPC** (usually default)
2. Select **at least 2–3 Availability Zones** (e.g., `us-east-1a`, `1b`, `1c`)
3. Click **Next**

---

## ✅ **Step 5: Load Balancer Configuration**

### You have three options:

* No Load Balancer
* Attach existing Load Balancer
* ✅ **Create new Load Balancer** → select this

Then:

1. **Type**: Application Load Balancer (ALB)
2. **Scheme**: Internet-facing
3. **Listener**: HTTP on port 80
4. **Target group**: Select **Create a target group**

Click **Next**

---

## ✅ **Step 6: Configure Scaling Policies**

1. **Desired capacity**: e.g., `2` (how many instances to start with)
2. **Minimum capacity**: e.g., `2`
3. **Maximum capacity**: e.g., `10`

Click **Next**

---

## ✅ **Step 7: Add Tags**

Add a name tag for instances:

* **Key**: `Name`
* **Value**: `asg-project-instance` (or any label)

Click **Next** → Click **Create Auto Scaling Group**

---

## ✅ **Step 8: Validation**

1. Go to **EC2 > Instances** → Two instances should be launching
2. Open one of their **public IPs** → Your website should load
3. Test submitting form/data to validate connection to RDS
4. Go to **Load Balancer** → Copy the DNS → Paste in browser

You should see your same website. That means load balancer is working and directing traffic to your instances.

---

## 🔄 **Behind the Scenes**

* AMI was used to replicate EC2 instance
* Launch template defines instance configuration
* ASG creates and manages EC2 instances automatically
* Load balancer distributes traffic across those instances
* Target group connects load balancer to instances

---

## 🧠 **Common Questions Answered**

**Q: What is the difference between AMI and Launch Template?**

* **AMI** contains the OS and software configuration (snapshot).
* **Launch Template** tells AWS how to launch the instance using that AMI.

**Q: What happens if the AMI isn't visible?**

* Wait a few minutes. Go to **AMIs → Refresh** until status is “Available”.

**Q: Why create AMI instead of Launch Template directly?**

* AMI ensures software state is preserved. Launch Template only stores hardware specs unless an AMI is attached.

**Q: Can I still follow the steps if my AMI isn't ready yet?**

* Yes, create the Launch Template first; you can select the AMI once it’s ready.

---
