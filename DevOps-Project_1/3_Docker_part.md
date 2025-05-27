# Dockerizing and Automating Deployment with Jenkins

## Objective

This document describes the process of forking a repository, creating a Dockerfile to containerize an application, configuring Jenkins jobs to build and deploy the project, and exposing the application on a browser.

## Steps

### Step 1: Fork the Repository

1. **Fork the Repository**: After forking the repository, go to the forked version of the repository to start working on it.
2. **Create a Dockerfile**: As the repository does not contain a Dockerfile, you will need to create it from scratch.
   - Name the file as `Dockerfile`.
   - Inside the Dockerfile, define the necessary instructions:
     - **Pull a base image**: Use Ubuntu as the base image.
     - **Install Apache Web Server**: Install Apache inside the container.
     - **Copy the index.html**: Replace the Apache default HTML page with the one from the repository.
     - **Start Apache**: Run Apache in the foreground when the container starts.

#### Sample Dockerfile

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

### Step 2: Set Up Webhook in GitHub

1. **Create a Webhook**: In GitHub, configure a webhook to notify Jenkins of changes to the repository.
   - Go to the repository settings and find the **Webhooks** section.
   - Add the Jenkins webhook URL and ensure that the payload is set to trigger on push events.
   - Click **Add Webhook**.

### Step 3: Configure Jenkins Job for Test Server

1. **Create Jenkins Job (Job 1)**:
   - Go to Jenkins dashboard, click on **New Item**, and create a **Freestyle Project**.
   - Name the job as **job1**.
   - Under **GitHub Project**, enter the repository URL.
   - **Restrict where this project can be run**: Select the label **test** (to run the job on the test server).
   - Under **Branch Specifier**, select **master**.
   - Enable **GitHub hook trigger for GITScm polling**.
   - Save the job.

2. **Build the Job**: Jenkins will automatically start the build once changes are detected in the repository.

### Step 4: Configure Jenkins Job for Development Branch

1. **Create Jenkins Job (Job 2)**:
   - Similar to Job 1, but this time configure it to work with the **develop** branch.
   - Change the **Branch Specifier** to **develop**.
   - Enable **GitHub hook trigger for GITScm polling**.
   - Save the job.

2. **Build the Job**: This job will not be triggered automatically until changes are made to the **develop** branch. Manually trigger the build to see it in action.

### Step 5: Configure Jenkins Job for Production Deployment

1. **Create Jenkins Job (Job 3)**:
   - Go to Jenkins dashboard, click on **New Item**, and create another **Freestyle Project**.
   - Name the job as **job3**.
   - **Restrict where this project can be run**: Select the label **prod** (to run the job on the production server).
   - Under **Branch Specifier**, choose **master**.
   - Enable **GitHub hook trigger for GITScm polling**.
   - Add a **Build Step** to **Execute Shell** to run Docker commands for building and running the Docker container.

#### Sample Shell Script for Job 3

```bash
# Build the Docker image from the Dockerfile
sudo docker build -t new_img .

# Run the Docker container with a specific port
sudo docker run -it -d -p 93:80 --name sc1 new_img
```

2. **Build the Job**: Once the job is triggered, Jenkins will build the Docker image and run it as a container.

### Step 6: Access the Application in a Browser

- After deploying the container, you should be able to access the application in the browser using the designated port (e.g., `http://localhost:93`).

--- 

### 6. **Handling Errors**
   - **Port Binding Error**:
     - When running the Docker container multiple times, an error occurred because port `93` was already bound to a running container.
     - **Solution**: A `docker rm -f` command was added to remove the previously created container before creating a new one:
       ```bash
       sudo docker rm -f sc1
       ```
     - This ensured that the port was freed for use by the new container.

---

### 8. **Testing the Deployment**
   - After making a change to the `index.html` file (e.g., adding the line `"Hello, this is project one!"`), the change was committed to the repository.
   - The webhook detected the change, which triggered the Jenkins build automatically.
   - The deployment was automatically updated, and visiting the public IP of the Prod server displayed the updated message on the browser.


