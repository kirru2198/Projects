## 📝 **Session Notes: SNS Message from EC2 Using CLI and CloudFormation**

### 👋 Good Morning & Setup

**Instructor:**

> Good morning, everyone. Thais, can you hear me? Please confirm.
> Okay, good. Now, you can find the "Project 3" file in the **media section** of your LMS. Please go through it.
> Give me a moment while I give you all the necessary access.

**Instructor:**

> Are you able to see my screen? Please confirm.

**Participants:**

> Yes, we can see your screen.
> Yes, we can hear you.

---

### 📄 **Project 3 Overview**

**Instructor:**

> As you can see on the screen, this is your **Project 3**. Please read the question and start thinking about the solution and how to implement it.
> We’ll wait a few more minutes for others to join. In the meantime, go through the question and plan your approach.

---

### ❓ SNS Trigger Clarification

**Dipa:**

> When will the SNS be triggered? For example, we need a trigger to send the notification, right? If we want to send reports or something, is it triggered when uploaded?

**Instructor:**

> No. SNS does not work on events directly like Lambda.
> SNS is just a **messaging service**. You must **explicitly send** a message to SNS for it to deliver it to its subscribers.
> Unlike Lambda, SNS is not automatically triggered by events unless you configure it through other services like S3 or CloudWatch.

**Dipa:**

> So in this case, if we’re sending reports online through SNS, are we triggering it when files are uploaded to S3?

**Instructor:**

> Not in this scenario.
> Here, the SNS message is sent **manually from the EC2 instance** using the **AWS CLI**.
> There is **no automatic trigger**. You will run a command or script on the EC2 to publish the message.

**Dipa:**

> Got it. So the EC2 machine sends the SNS message using AWS CLI.

---

### 🎯 Today's Agenda

**Instructor:**

> ✅ Today's focus is: **Sending an SNS message from an EC2 instance privately using AWS CLI**.
> Normally, we publish messages from the SNS dashboard. Today, we’ll do it **from inside the EC2 instance**, simulating how real-time backend servers operate.

> Your EC2 instance (acting as the backend) will use AWS CLI to send messages to an SNS topic.
> The **SNS topic** may have subscribers like:
>
> * Lambda functions
> * Email addresses
> * SMS, etc.

> All AWS resources (VPC, EC2, SNS topic, etc.) will be created using **CloudFormation**.

---

### 🛠️ How to Launch a Public EC2 Instance in a VPC?

**Instructor:**

> Let’s revise: If I want to launch a **public EC2 instance** in a VPC, what steps would I follow on the AWS Dashboard?

**Participant:**

> First, create the **VPC**.
> Then create the **subnet**, followed by the **Internet Gateway** and **Route Table**.
> Associate the subnet with the route table and add a route to the Internet Gateway.
> Finally, launch the EC2 instance into the subnet and attach a **Security Group** to it.

**Instructor:**

> Correct. Here's a breakdown of the standard steps:

---

### ✅ Steps to Launch a Public EC2 Instance

1. **Create a VPC**
2. **Create an Internet Gateway**
3. **Attach the Internet Gateway to the VPC**
4. **Create a Subnet** (within the VPC)
5. **Create a Route Table**
6. **Associate Subnet with the Route Table**
7. **Add Route in Route Table to the Internet Gateway (0.0.0.0/0)**
8. **Create a Security Group** (allow SSH, HTTP, etc.)
9. **Launch EC2 Instance in the Subnet**
10. **Assign Public IP to EC2 Instance**

---

### 📦 Using CloudFormation

**Instructor:**

> The same steps you follow manually in the dashboard must be **replicated using CloudFormation templates**.

> That’s why it's important to **understand the manual steps first**—CloudFormation will mirror those.

---
