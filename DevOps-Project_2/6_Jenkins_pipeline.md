### Simplified Notes on Jenkins and Kubernetes Setup

1. **Environment Setup**:
   - Docker has been installed on the Kubernetes master and slave machines.
   - Jenkins is set up on a separate machine (Jenkins M).

2. **Launching Jenkins**:
   - Access the Jenkins dashboard using the IP address and port of the Jenkins M machine.
   - Copy the initial admin password from the Jenkins dashboard and paste it to log in.
   - Select "Install suggested plugins" and wait for the installation to complete.
   - Create a username and save the configuration to access the Jenkins dashboard.

3. **Configuring Jenkins Node**:
   - Navigate to "Manage Jenkins" > "Nodes" to create a new node for the Kubernetes master machine.
   - Name the node (e.g., "km") and configure the remote root directory (e.g., `/home/ubuntu`).
   - Set the launch method to SSH and provide the private IP of the Kubernetes master.
   - Add SSH credentials using the private key from the `.pem` file.
   - Save the configuration and verify that the node is launched successfully.

4. **Creating a Jenkins Job**:
   - Create a new job and select "Pipeline" as the job type.
   - Fork the GitHub repository for the project and set up a webhook to connect Jenkins with GitHub.
   - Create a `Dockerfile` in the repository with the necessary commands to build the application.

5. **Pipeline Script Configuration**:
   - In the Jenkins job configuration, write a pipeline script to define the build process.
   - Set the agent to `none` and define environment variables for Docker Hub credentials.
   - Create stages in the pipeline for:
     - Cloning the GitHub repository.
     - Building the Docker image.
     - Logging into Docker Hub.
     - Pushing the Docker image to Docker Hub.

6. **Deployment Configuration**:
   - Create a `deployment.yaml` file in the GitHub repository for Kubernetes deployment.
   - Add the necessary deployment specifications in the YAML file.
   - Update the Jenkins pipeline script to apply the deployment and service YAML files using `kubectl apply -f`.

7. **Final Steps**:
   - Save all changes in Jenkins and build the job to execute the pipeline.
   - Verify that the Docker image is pushed to Docker Hub and that the Kubernetes deployment is successful.

### Key Commands in the Pipeline Script:
- **Docker Build**: 
  ```bash
  sudo docker build -t <username>/<image_name> .
  ```
- **Docker Login**:
  ```bash
  sudo docker login -u $DOCKER_HUB_CREDENTIALS_ID -p $DOCKER_HUB_CREDENTIALS_PSW
  ```
- **Docker Push**:
  ```bash
  sudo docker push <username>/<image_name>
  ```
- **Kubernetes Apply**:
  ```bash
  kubectl apply -f deployment.yaml
  ```

This summary captures the essential steps and commands for setting up Jenkins with Kubernetes, creating a pipeline, and deploying applications.

---

### Simplified Notes on Kubernetes Deployment and Jenkins Pipeline

1. **Editing Deployment Configuration**:
   - Change the number of replicas in the `deploy.yaml` file from 3 to 2.
   - Update the deployment name from "nginx-deployment" to "my-deployment".
   - Specify the Docker image pushed to Docker Hub: `aadi/project-two`.

2. **Creating Service Configuration**:
   - Create a `service.yaml` file for the service configuration.
   - Use the NodePort service type and simplify the app label to just "app".
   - Remove unnecessary comments from the service configuration.

3. **Pipeline Script Updates**:
   - In the Jenkins pipeline script, add commands to apply the deployment and service configurations:
     ```bash
     kubectl apply -f deploy.yaml
     kubectl apply -f service.yaml
     ```
   - Ensure the commands are executed in the Kubernetes agent labeled "km".

4. **Deleting Previous Deployments**:
   - To avoid cluttering with multiple deployments and services, delete previous instances before reapplying:
     ```bash
     kubectl delete deployment my-deployment
     kubectl delete service my-service
     ```
   - This should be added to the pipeline script before applying the new configurations.

5. **Building the Job**:
   - Build the Jenkins job to apply the changes.
   - Verify the deployment and service using:
     ```bash
     kubectl get deployments
     kubectl get services
     ```

6. **Webhook Implementation**:
   - A webhook is set up to automatically trigger a Jenkins build when changes are made to the GitHub repository.
   - For example, editing the `index.html` file will trigger a new build in Jenkins.

7. **Final Checks**:
   - After making changes, ensure that the deployment and service are correctly configured and visible in Kubernetes.
   - Monitor the Jenkins dashboard for successful builds and logs.

8. **Resources and Documentation**:
   - Links to installation commands, script files, and YAML configurations are shared for reference.
   - Practice the project multiple times to gain familiarity with the setup and commands.

### Key Commands:
- **Kubernetes Commands**:
  ```bash
  kubectl apply -f deploy.yaml
  kubectl apply -f service.yaml
  kubectl delete deployment my-deployment
  kubectl delete service my-service
  kubectl get deployments
  kubectl get services
  ```

This summary captures the essential steps and commands for configuring Kubernetes deployments and services, integrating with Jenkins, and managing the pipeline effectively.
