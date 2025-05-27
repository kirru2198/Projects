## 🔧 **Project Objective Summary**

You are hired as a **DevOps Engineer** at a product-based company that:

* Previously used **monolithic architecture**
* Has experienced a **surge in user demand**
* Uses **Docker** for containerization
* Needs **automated deployment, scaling, and operations** using **Kubernetes**
* Wants **zero change** in existing Docker containers

---

## 🧩 **Tech Stack to Use**

* **GitHub**: Version control, Git workflow, webhooks
* **Jenkins**: CI/CD automation (pipeline)
* **Docker**: Containerization
* **Docker Hub**: Image repository
* **Kubernetes**: Orchestration (using YAML)
* **Ansible (optional)**: Infrastructure configuration management

---

## ✅ **Task-by-Task Implementation Plan**

### 🟩 **Task 1: GitHub Workflow**

* **Fork** the given repository.
* Create necessary files:

  * `Dockerfile`
  * `deployment.yaml`
  * `service.yaml`
  * Optional: `.jenkinsfile` or `Jenkinsfile` for pipeline
* Enable **GitHub Webhook** to Jenkins (detect repo changes).
* Use **feature branches**, commits, pull requests for Git workflow.

---

### 🟩 **Task 2: Jenkins Webhook Trigger**

* Integrate GitHub with Jenkins via **webhooks**.
* Configure Jenkins to:

  * Trigger on `master` branch commits.
  * Pull latest code.
  * Execute the pipeline automatically.

---

### 🟩 **Task 3: Docker Containerization**

* Create a **Dockerfile** that packages the app (e.g., using `index.html` or web app).
* In Jenkins Pipeline:

  * `docker build -t yourusername/appname .`
  * `docker push yourusername/appname`

> ✅ Push the built Docker image to **Docker Hub**.

---

### 🟩 **Task 4: Kubernetes Deployment (2 Replicas)**

* Use the pushed Docker image from Docker Hub in `deployment.yaml`.

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: product
  template:
    metadata:
      labels:
        app: product
    spec:
      containers:
      - name: product-container
        image: yourusername/appname:latest
        ports:
        - containerPort: 80
```

**service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: product-service
spec:
  type: NodePort
  selector:
    app: product
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080  # As mentioned in the project
```

---

### 🟩 **Task 5: Jenkins Pipeline Script**

* Use a **declarative Jenkinsfile** or scripted pipeline:

**Sample `Jenkinsfile`:**

```groovy
pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
  }
  stages {
    stage('Clone Repository') {
      steps {
        git branch: 'master', url: 'https://github.com/yourusername/repo.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t yourusername/appname .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
          sh 'docker push yourusername/appname'
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f deployment.yaml'
        sh 'kubectl apply -f service.yaml'
      }
    }
  }
}
```

---

### 🟩 **Task 6 (Optional): Ansible for Config Management**

* Use **Ansible playbooks** to:

  * Install Docker/Kubernetes
  * Setup Jenkins on remote machines
  * Pull and deploy code via Kubernetes on remote hosts

---

### 🟩 **Task 7+: CI/CD Validation**

* Validate the full flow:

  * Git commit → GitHub push → Jenkins trigger → Docker build & push → Kubernetes deploy

---

## 📌 **Additional Notes**

* Ensure:

  * Jenkins has `Docker`, `kubectl`, `Git` installed
  * Docker Hub credentials are stored securely in Jenkins
  * Kubernetes cluster is accessible from Jenkins (via kubeconfig)
* Use `nodePort: 30080` (as mentioned)
* Check `docker ps`, `kubectl get pods`, `kubectl get svc` for debugging

---


