1.Create two custom VPCs in the Oregon region:
–> VPC-A (name: PRT-App-VPC)
–> VPC-B (name: PRT-Data-VPC)
2.Each VPC should have 1 Public and 1 Private subnet.
3.Launch one Amazon Linux EC2 instance in the private subnet of each VPC:
–> Name in VPC-A: App-Instance
–> Name in VPC-B: Data-Instance
4.Configure the Security Group of App-Instance that it can only communicate with Data-Instance.
5.Configure a VPC Peering Connection between the two VPCs and update the route tables accordingly to enable communication between the private subnets.
6. In VPC-B, create an S3 Gateway VPC Endpoint and ensure the Data-Instance can access S3 without using the internet.
Screenshots required:
1.Screenshot(s) of both VPC Resource Maps
2.Screenshot(s) of Instance Summary for App-Instance and Data-Instance
3.Screenshot(s) of the VPC Peering Connection (status and routing)
4.Screenshot(s) of the VPC Endpoint Configuration and its associated route table
5.Screenshot(s) of the App-Instance able to communicate from Data-Instance only.
6.Screenshot(s) showing S3 access from Data-Instance
7.Screenshot(s) of Security Group rules for both instances

---

# AWS VPC Lab: Complete Step-by-Step Guide

## Overview
This lab demonstrates creating a multi-VPC architecture with secure communication between instances and S3 access through VPC endpoints.

## Architecture Overview
- **VPC-A (PRT-App-VPC)**: Contains App-Instance in private subnet
- **VPC-B (PRT-Data-VPC)**: Contains Data-Instance in private subnet
- **VPC Peering**: Enables communication between the two VPCs
- **S3 VPC Endpoint**: Allows private S3 access from Data-Instance

---

## Step 1: Create VPC-A (PRT-App-VPC)

### 1.1 Create the VPC
1. Go to **AWS Console** → **VPC** → **Your VPCs** → **Create VPC**
2. Configuration:
   - **Name tag**: PRT-App-VPC
   - **IPv4 CIDR block**: 10.0.0.0/16
   - **IPv6 CIDR block**: No IPv6 CIDR block
   - **Tenancy**: Default
3. Click **Create VPC**

### 1.2 Create Internet Gateway for VPC-A
1. Go to **Internet Gateways** → **Create internet gateway**
2. **Name tag**: PRT-App-IGW
3. Click **Create internet gateway**
4. **Attach to VPC**: Select PRT-App-VPC → **Attach internet gateway**

### 1.3 Create Subnets for VPC-A
**Public Subnet:**
1. Go to **Subnets** → **Create subnet**
2. Configuration:
   - **VPC ID**: Select PRT-App-VPC
   - **Subnet name**: PRT-App-Public-Subnet
   - **Availability Zone**: us-west-2a
   - **IPv4 CIDR block**: 10.0.1.0/24
3. Click **Create subnet**

**Private Subnet:**
1. **Create subnet** again
2. Configuration:
   - **VPC ID**: Select PRT-App-VPC
   - **Subnet name**: PRT-App-Private-Subnet
   - **Availability Zone**: us-west-2b
   - **IPv4 CIDR block**: 10.0.2.0/24
3. Click **Create subnet**

### 1.4 Create Route Tables for VPC-A
**Public Route Table:**
1. Go to **Route Tables** → **Create route table**
2. Configuration:
   - **Name**: PRT-App-Public-RT
   - **VPC**: PRT-App-VPC
3. Click **Create route table**
4. **Edit routes** → **Add route**:
   - **Destination**: 0.0.0.0/0
   - **Target**: Internet Gateway (PRT-App-IGW)
5. **Subnet Associations** → **Edit subnet associations** → Select PRT-App-Public-Subnet

**Private Route Table:**
1. **Create route table**
2. Configuration:
   - **Name**: PRT-App-Private-RT
   - **VPC**: PRT-App-VPC
3. **Subnet Associations** → Select PRT-App-Private-Subnet

---

## Step 2: Create VPC-B (PRT-Data-VPC)

### 2.1 Create the VPC
1. **Create VPC**
2. Configuration:
   - **Name tag**: PRT-Data-VPC
   - **IPv4 CIDR block**: 172.16.0.0/16
3. Click **Create VPC**

### 2.2 Create Internet Gateway for VPC-B
1. **Create internet gateway**
2. **Name tag**: PRT-Data-IGW
3. Attach to PRT-Data-VPC

### 2.3 Create Subnets for VPC-B
**Public Subnet:**
- **Subnet name**: PRT-Data-Public-Subnet
- **VPC**: PRT-Data-VPC
- **IPv4 CIDR block**: 172.16.1.0/24

**Private Subnet:**
- **Subnet name**: PRT-Data-Private-Subnet
- **VPC**: PRT-Data-VPC
- **IPv4 CIDR block**: 172.16.2.0/24

### 2.4 Create Route Tables for VPC-B
Follow similar steps as VPC-A:
- **PRT-Data-Public-RT** (with IGW route)
- **PRT-Data-Private-RT** (local routes only for now)

---

## Step 3: Launch EC2 Instances

### 3.1 Create Security Groups First

**App-Instance Security Group:**
1. Go to **Security Groups** → **Create security group**
2. Configuration:
   - **Security group name**: App-SG
   - **Description**: Security group for App Instance
   - **VPC**: PRT-App-VPC
3. **Outbound rules**: Add rule to allow communication to Data-Instance
   - **Type**: All Traffic
   - **Protocol**: All
   - **Port range**: All
   - **Destination**: 172.16.2.0/24 (Data-Instance subnet)

**Data-Instance Security Group:**
1. **Create security group**
2. Configuration:
   - **Security group name**: Data-SG
   - **VPC**: PRT-Data-VPC
3. **Inbound rules**: Allow communication from App-Instance
   - **Type**: All Traffic
   - **Source**: 10.0.2.0/24 (App-Instance subnet)
4. **Outbound rules**: Allow HTTPS for S3 access
   - **Type**: HTTPS
   - **Port**: 443
   - **Destination**: 0.0.0.0/0

### 3.2 Launch App-Instance
1. Go to **EC2** → **Launch Instance**
2. Configuration:
   - **Name**: App-Instance
   - **AMI**: Amazon Linux 2023
   - **Instance type**: t2.micro
   - **Key pair**: Select or create a key pair
   - **Network settings**:
     - **VPC**: PRT-App-VPC
     - **Subnet**: PRT-App-Private-Subnet
     - **Auto-assign public IP**: Disable
     - **Security group**: App-SG
3. Click **Launch instance**

### 3.3 Launch Data-Instance
1. **Launch Instance**
2. Configuration:
   - **Name**: Data-Instance
   - **AMI**: Amazon Linux 2023
   - **Instance type**: t2.micro
   - **Network settings**:
     - **VPC**: PRT-Data-VPC
     - **Subnet**: PRT-Data-Private-Subnet
     - **Security group**: Data-SG
3. Click **Launch instance**

---

## Step 4: Configure VPC Peering Connection

### 4.1 Create Peering Connection
1. Go to **VPC** → **Peering Connections** → **Create peering connection**
2. Configuration:
   - **Peering connection name**: PRT-VPC-Peering
   - **VPC (Requester)**: PRT-App-VPC
   - **VPC (Accepter)**: PRT-Data-VPC
3. Click **Create peering connection**

### 4.2 Accept Peering Connection
1. Select the peering connection
2. **Actions** → **Accept request**
3. Confirm acceptance

### 4.3 Update Route Tables
**Update PRT-App-Private-RT:**
1. Go to **Route Tables** → Select PRT-App-Private-RT
2. **Routes** → **Edit routes** → **Add route**:
   - **Destination**: 172.16.0.0/16
   - **Target**: Peering Connection (PRT-VPC-Peering)

**Update PRT-Data-Private-RT:**
1. Select PRT-Data-Private-RT
2. **Edit routes** → **Add route**:
   - **Destination**: 10.0.0.0/16
   - **Target**: Peering Connection (PRT-VPC-Peering)

---

## Step 5: Create S3 Gateway VPC Endpoint

### 5.1 Create VPC Endpoint
1. Go to **VPC** → **Endpoints** → **Create endpoint**
2. Configuration:
   - **Name tag**: PRT-S3-Endpoint
   - **Service category**: AWS services
   - **Service name**: com.amazonaws.us-west-2.s3 (Gateway)
   - **VPC**: PRT-Data-VPC
   - **Route tables**: Select PRT-Data-Private-RT
3. Click **Create endpoint**

---

## Step 6: Testing and Verification

### 6.1 Test Instance Communication
You'll need a bastion host or Systems Manager Session Manager to access private instances.

**Option 1: Create Bastion Host**
1. Launch a small EC2 instance in the public subnet of either VPC
2. SSH to bastion, then SSH to private instances

**Option 2: Use Systems Manager Session Manager**
1. Create IAM role with SSM permissions
2. Attach role to instances
3. Use Session Manager to connect

### 6.2 Test App-Instance to Data-Instance Communication
```bash
# From App-Instance, ping Data-Instance
ping 172.16.2.x  # Replace with actual Data-Instance IP

# Test port connectivity
telnet 172.16.2.x 22
```

### 6.3 Test S3 Access from Data-Instance
```bash
# Install AWS CLI if not present
sudo yum update -y
sudo yum install aws-cli -y

# Configure AWS CLI (use IAM role preferred)
aws configure

# Test S3 access
aws s3 ls
aws s3 mb s3://your-test-bucket-name-unique
aws s3 cp /etc/hosts s3://your-test-bucket-name-unique/
```

---

## Required Screenshots Checklist

1. **VPC Resource Maps**: 
   - VPC Dashboard showing both VPCs with subnets
   - Network topology view

2. **Instance Summary**:
   - EC2 Dashboard showing both instances
   - Instance details showing VPC and subnet info

3. **VPC Peering Connection**:
   - Peering connection status (Active)
   - Route table entries showing peering routes

4. **VPC Endpoint Configuration**:
   - S3 endpoint details
   - Associated route table showing S3 endpoint routes

5. **Instance Communication**:
   - Terminal showing successful ping/connection from App to Data instance
   - Network connectivity tests

6. **S3 Access**:
   - AWS CLI commands showing S3 access from Data-Instance
   - S3 bucket operations working

7. **Security Group Rules**:
   - App-SG outbound rules
   - Data-SG inbound rules
   - Both security groups showing proper configuration

---

## Common Troubleshooting Tips

1. **Can't connect to instances**: Check NACLs, Security Groups, and route tables
2. **Peering not working**: Verify route table entries in both VPCs
3. **S3 access failing**: Check IAM permissions and VPC endpoint policy
4. **DNS resolution issues**: Enable DNS resolution in VPC settings

---

## Cost Optimization Notes

- Use t2.micro instances (free tier eligible)
- Delete resources after practice to avoid charges
- VPC Peering and VPC Endpoints have minimal costs
- Consider using CloudFormation for easy cleanup

---

## Next Steps for Learning

1. Practice with CloudFormation templates
2. Learn about Transit Gateway for complex networking
3. Explore Network Load Balancers
4. Study AWS PrivateLink for service connectivity
