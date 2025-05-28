## 🔧 **Capstone Project Summary**

### 🎯 **Goal**:

Automate the **deployment, scaling, and operations** of an application using **GitHub, Jenkins, Docker, and Kubernetes** — without altering the existing Docker container in the testing environment.

---

## 📝 **Project Requirements Breakdown**

### **1. Git Workflow Implementation**

* Fork the provided GitHub repository (monolithic application).
* Create feature branches for changes.
* Add required files like:

  * `Dockerfile`
  * `deployment.yaml`
  * `service.yaml`
  * `Jenkinsfile`
* Enable **webhooks** to trigger Jenkins on code changes (especially on `master` branch).

---

### **2. Jenkins CI/CD Integration**

* Configure **webhooks** in GitHub to notify Jenkins.
* In Jenkins:

  * Use a **Pipeline project** (not freestyle).
  * Create a `Jenkinsfile` to:

    * Detect code changes.
    * Build the Docker image.
    * Push image to Docker Hub.
    * Deploy to Kubernetes using `kubectl` and `yaml` files.

---

### **3. Docker Containerization**

* Write a `Dockerfile` to:

  * Containerize the app (e.g., an `index.html` or web app).
  * Ensure it exposes the correct port (`80` or custom as needed).
* Build the Docker image:

  ```sh
  docker build -t <your-dockerhub-username>/your-image-name:tag .
  ```
* Push the image to Docker Hub:

  ```sh
  docker push <your-dockerhub-username>/your-image-name:tag
  ```

---

### **4. Kubernetes Deployment**

* Create `deployment.yaml`:

  * Use the image pushed to Docker Hub.
  * Set `replicas: 2`.
  * Include proper labels/selectors.

* Create `service.yaml`:

  * Type: `NodePort`.
  * Port: Use a specific port (e.g., `30008`) as per the requirement.
  * Link to the deployment using labels.

---

### **5. Jenkins Pipeline Script**

* Write a `Jenkinsfile` using **Declarative or Scripted Pipeline**.
* Example structure:

  ```groovy
  pipeline {
    agent any

    environment {
      DOCKER_IMAGE = "your-dockerhub-username/your-image-name:tag"
    }

    stages {
      stage('Clone Repo') {
        steps {
          git 'https://github.com/your-org/your-repo.git'
        }
      }

      stage('Build Docker Image') {
        steps {
          sh 'docker build -t $DOCKER_IMAGE .'
        }
      }

      stage('Push Image to Docker Hub') {
        steps {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh '''
              echo $PASSWORD | docker login -u $USERNAME --password-stdin
              docker push $DOCKER_IMAGE
            '''
          }
        }
      }

      stage('Deploy to Kubernetes') {
        steps {
          sh '''
            kubectl apply -f deployment.yaml
            kubectl apply -f service.yaml
          '''
        }
      }
    }
  }
  ```

---

### **6. Key Notes**

* Use **Docker Hub credentials** securely (via Jenkins credentials).
* Ensure `kubectl` is configured on Jenkins (or use a Kubernetes agent).
* You can test everything locally using **minikube** or **kind**, or in a cloud environment.

---

## ✅ Final Deliverables:

* Forked GitHub repo with:

  * `Dockerfile`
  * `deployment.yaml`
  * `service.yaml`
  * `Jenkinsfile`
* Jenkins pipeline job linked with GitHub (webhook enabled).
* Working Docker image pushed to Docker Hub.
* Running Kubernetes deployment with NodePort access.

---
