# Project Overview and Solution Walkthrough

In this project, we focused on Dockerizing an application, automating builds using Jenkins, and deploying the application to different servers (Test, Prod) for showcasing the deployment pipeline. We used various tools such as Jenkins, GitHub, Docker, and Jenkins webhooks to trigger builds based on changes in the repository.

---

## Step-by-Step Solution

### 1. **Repository Setup**
   - Fork the repository and configure it for Dockerization.
   - The Dockerfile was created from scratch, as it wasn't initially available in the repository.
   
### 2. **Creating a Dockerfile**
   - The first task was to create a `Dockerfile` for containerizing the files in the repository. The steps included:
     - **Pulling the base image**: We used `FROM ubuntu` to pull an Ubuntu image.
     - **Installing Apache Web Server**: The web server was installed using the command:
       ```bash
       RUN apt-get update && apt-get install -y apache2
       ```
     - **Adding the index.html file**: We added the custom `index.html` file to the container's web server directory:
       ```bash
       ADD index.html /var/www/html/index.html
       ```
     - **Starting Apache in the foreground**: This was done using the `ENTRYPOINT` command:
       ```bash
       ENTRYPOINT ["apache2ctl", "-D", "FOREGROUND"]
       ```

### 3. **GitHub Webhook Configuration**
   - To enable Jenkins to trigger builds on changes to the repository, a webhook was set up in the GitHub repository settings.
   - The webhook was linked to Jenkins, and any changes in the repository triggered Jenkins jobs.

### 4. **Jenkins Job Configuration**
   - **Job 1** (Test Server):
     - Job 1 was configured to build the application using the `master` branch and deploy it to a Test server.
     - The configuration included setting the GitHub repository URL, specifying the branch (`master`), and enabling the GitHub webhook trigger.
   - **Job 2** (Test Server for Develop Branch):
     - Similar to Job 1, but Job 2 worked with the `develop` branch, and the built files were deployed to the same Test server.
   - **Job 3** (Prod Server):
     - This job was responsible for deploying the application to the Prod server.
     - The configuration used the `master` branch for building and exposing the deployed application on a browser.

### 5. **Deployment on Prod Server**
   - **Containerizing the Application**:
     - In Job 3, Docker commands were used to build and run the container, exposing the application on port 93.
     - Example Docker commands:
       ```bash
       sudo docker build -t new-img .
       sudo docker run -it -d -p 93:80 --name sc1 new-img
       ```
   - The application was exposed to the public IP of the Prod server on port 93. Accessing the Prod server's public IP and port displayed the deployed application.

### 6. **Handling Errors**
   - **Port Binding Error**:
     - When running the Docker container multiple times, an error occurred because port `93` was already bound to a running container.
     - **Solution**: A `docker rm -f` command was added to remove the previously created container before creating a new one:
       ```bash
       sudo docker rm -f sc1
       ```
     - This ensured that the port was freed for use by the new container.

### 7. **Automation of Jenkins Build**
   - Once the webhooks were set up, changes made to the repository (e.g., modifying `index.html`) were automatically detected by Jenkins and triggered builds without manual intervention.
   - This allowed the automated building and deployment process to run smoothly on both the Test and Prod servers.

### 8. **Testing the Deployment**
   - After making a change to the `index.html` file (e.g., adding the line `"Hello, this is project one!"`), the change was committed to the repository.
   - The webhook detected the change, which triggered the Jenkins build automatically.
   - The deployment was automatically updated, and visiting the public IP of the Prod server displayed the updated message on the browser.

---

## Summary of Key Concepts

- **Dockerization**: Containerizing the application with a Dockerfile to run it in a controlled environment.
- **Jenkins Automation**: Automating builds and deployments using Jenkins, with webhook triggers from GitHub for continuous integration.
- **Error Handling in Docker**: Managing port conflicts and ensuring that old containers are removed before starting new ones.
- **Deployment**: Deploying the containerized application to a Test and Prod server, with proper exposure of the application on a browser.

---

## Resources & Commands Shared

Below are the resources and command snippets shared during the session:

1. **Ansible Installation Command**:
   ```bash
   sudo apt-get install ansible
   ```

2. **Jenkins Master Setup**:
   - To install Jenkins and configure it as the master server.

3. **Jenkins Slave Setup**:
   - Steps to configure Jenkins slaves to distribute the workload.

4. **Docker Build and Run Commands**:
   - For building the Docker image:
     ```bash
     sudo docker build -t new-img .
     ```
   - For running the Docker container:
     ```bash
     sudo docker run -it -d -p 93:80 --name sc1 new-img
     ```

5. **Playbook Script**: 
   - The playbook script was shared for automating the setup tasks.

---

## Conclusion

This project demonstrated a typical CI/CD pipeline using Jenkins and Docker for building, deploying, and managing applications. By integrating GitHub webhooks, Jenkins jobs, and Docker, we created an automated deployment pipeline that was able to deploy applications seamlessly to Test and Prod servers.

Feel free to practice these steps and reach out via email for any issues or questions that arise during your implementation.

---

If you have any further doubts or need clarification, please reach out! 😊
