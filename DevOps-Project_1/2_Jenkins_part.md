---

# Jenkins Setup and Node Configuration for DevOps Project

### Introduction

In this section, we will walk through the process of setting up Jenkins on the Jenkins Master machine, configuring the necessary nodes (Slave Machines), and creating jobs for building and deploying the application.

### Step 1: Launch Jenkins Dashboard

We have installed Jenkins on the Jenkins Master machine. Now, to access the Jenkins dashboard:

1. **Copy the public IP of the Jenkins Master machine**.
2. Open a new tab in your browser and paste the public IP followed by `:8080` to access the Jenkins dashboard.
   - Example: `http://<Jenkins_Master_IP>:8080`

Once you access the Jenkins dashboard, it will prompt you for an administrator password.

### Step 2: Fetch the Administrator Password

To retrieve the Jenkins admin password:

1. On the Jenkins Master machine (which is where Jenkins is installed), run the following command:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
2. **Copy the admin password** that is displayed in the terminal.

### Step 3: Install Suggested Plugins

1. Paste the admin password into the Jenkins setup page to log in.
2. Click **Install Suggested Plugins** to install the necessary plugins for Jenkins.

### Step 4: Set Up Jenkins User

1. After plugin installation, you will be prompted to set up a Jenkins username and password.
2. Enter the username and password (e.g., your name or any desired username).

Click **Save and Finish**, and the Jenkins dashboard should now be ready.

---

### Step 5: Configure Jenkins Nodes (Slave Machines)

Now, we need to configure the nodes (Slave machines) for Jenkins. We will set up two nodes:

1. **Test Node**: This will be associated with `Slave 1` for building and testing code from the `develop` branch.
2. **Prod Node**: This will be associated with `Slave 2` for deploying the application to the production environment.

#### **Configuring Test Node (Slave 1)**

1. Go to **Manage Jenkins** → **Manage Nodes**.
2. Click **New Node**.
3. Provide the node name as `test` and select **Permanent Agent**.
4. Click **OK** to proceed to the node configuration.
5. Set the following configurations:
   - **Remote Root Directory**: `/home/ubuntu/jenkins`
   - **Label**: `test`
   - **Launch method**: **Launch via SSH**
   - **Host**: Enter the private IP address of the `Slave 1` machine.
   - **Credentials**: Choose **SSH Username with private key**. Add the private key content from the `Slave 1` machine.
   - **Host verification strategy**: choose to Non verifying.
   
6. Click **Save** to create the node.

After saving, the `test` node should appear in the **Available** state on the **Manage Nodes** page.

#### **Configuring Prod Node (Slave 2)**

1. Click **New Node** again to create the `prod` node.
2. Provide the node name as `prod` and select **Permanent Agent**.
3. Click **OK** to proceed to the node configuration.
4. Set the following configurations:
   - **Remote Root Directory**: `/home/ubuntu/jenkins`
   - **Label**: `prod`
   - **Launch method**: **Launch via SSH**
   - **Host**: Enter the private IP address of the `Slave 2` machine.
   - **Credentials**: Choose **SSH Username with private key**. Use the same private key content from the `Slave 1` machine.
   - **Host verification strategy**: choose to Non verifying.

5. Click **Save** to create the node.

### Step 6: Troubleshooting Node Launch

- If a node does not launch properly, verify the private IP and ensure the correct SSH keys are configured for both nodes.
- If the node fails to connect, check that the **Host Key Verification Strategy** is set to **Non-verifying** (since this disables strict host key verification).
