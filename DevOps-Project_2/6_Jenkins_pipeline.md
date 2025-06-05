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
   - Name the node (e.g., "km") and configure the remote root directory (e.g., `/home/ubuntu/jenkins`).
   - Set the launch method to SSH and provide the private IP of the Kubernetes master.
   - Add SSH credentials using the private key from the `.pem` file.
   - Save the configuration and verify that the node is launched successfully.

4. **Creating a Jenkins Job**:
   - Create a new job and select "Pipeline" as the job type.
   - Fork the GitHub repository for the project and set up a webhook to connect Jenkins with GitHub.
   - Create a `Dockerfile` in the repository with the necessary commands to build the application.

```
FROM ubuntu
RUN apt-get update
RUN apt-get install apache2 -y
ADD . /var/www/html/
ENTRYPOINT apachectl -D FOREGROUND
```

5. **Pipeline Script Configuration**:
   -  on the Dashboard select the Github hook trigger for GITScm Polling
   - In the Jenkins job configuration, write a pipeline script to define the build process.
   - Set the agent to `none` and define environment variables for Docker Hub credentials.
   - Create stages in the pipeline for:
     - Cloning the GitHub repository.
     - Building the Docker image.
     - Logging into Docker Hub.
     - Pushing the Docker image to Docker Hub.

7. **Deployment Configuration**:
   - Create a `deployment.yaml` file in the GitHub repository for Kubernetes deployment.
   - Add the necessary deployment specifications in the YAML file.
   - Update the Jenkins pipeline script to apply the deployment and service YAML files using `kubectl apply -f`.

8. **Final Steps**:
   - Save all changes in Jenkins and build the job to execute the pipeline.
   - Verify that the Docker image is pushed to Docker Hub and that the Kubernetes deployment is successful.

---

### For getting Docker Hub Credentials id 

- go to the Jenkins Dashboard
- > Manage Jenkins > Credentials > Click on (global) > Click on Add Credentials
  > Username = Usernmane of Dockerhub account
  > Password = of Dockerhub account
  > Click on Create

- Copy the id of credentials and past that in Dockerhub Credentials part of Pipeline

### `Jenkinsfile`

```groovy
pipeline {
    agent none
    environment {
        DOCKERHUB_CREDENTIALS = credentials("cb26ac19-f954-40ca-a12e-d790594bcca7")
    }
    stages {
        stage('git') {
            agent {
                label "K8-Master"
            }
            steps {
                script {
                    git 'https://github.com/Sameer-8080/website.git'
                }
            }
        }
        stage('docker') {
            agent {
                label "K8-Master"
            }
            steps {
                script {
                    sh 'sudo docker build /home/ubuntu/jenkins/workspace/test-pipeline/ -t intellipaatsai/proj2'
                    sh 'sudo docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}'
                    sh 'sudo docker push intellipaatsai/proj2'
                }
            }
        }
        stage('kubernetes') {
            agent {
                label "K8-Master"
            }
            steps {
                script {
                    sh 'kubectl delete deploy nginx-deployment'
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl delete service my-service'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
```

---

### `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-deployment
  template:
    metadata:
      labels:
        app: my-deployment
    spec:
      containers:
      - name: my-deployment
        image: aadi0410/project-2
        ports:
        - containerPort: 80
```

---

### `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-deployment
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```
---

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
