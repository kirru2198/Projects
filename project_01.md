# DevOps Capstone Project 1

## Overview

In today's session, we will be discussing the **DevOps Capstone Project 1**. The task is based on implementing the **DevOps lifecycle** in a software company, which is a product-based company. The product code is stored in a repository, and we need to deploy this product. Let's walk through the steps involved in solving this problem.

---

### Problem Set

The company has hired us as **Senior DevOps Engineers** to implement the **DevOps lifecycle** as quickly as possible. The product’s code is hosted on a repository, and we need to deploy it efficiently.

1. **Code Repository**: All the code that the company has developed is stored in the repository (the link to this repository is provided in the problem set).
2. **Objective**: We need to focus on deploying the product using the DevOps lifecycle.
  
---

### Task Breakdown

#### Task 1: Installing Necessary Software Using Configuration Management Tools

- **Configuration Management Tool**: We are required to use a **configuration management tool** to install the necessary software on the machines. 
- **Tool of Choice**: **Ansible** is the tool we'll be using for this purpose.
  
  **Question**: What is a configuration management tool?
  - **Answer**: A configuration management tool automates the process of managing and configuring servers. Ansible helps in automating software installation and configurations across multiple servers.

---

#### Task 2: Implementing the GitLab (GL) Workflow

- **Objective**: We need to focus on implementing the **GitLab workflow**. This involves:
  - Creating branches for development (e.g., `develop`, `master`).
  - Creating a **Dockerfile** for the application.
  
  **GL Workflow** means managing the repositories and branches, and ensuring the application is properly containerized for deployment.

---

#### Task 3: Automated Code Build on Commit

- **Trigger**: The build should be triggered automatically once a commit is made to either the **master** or **develop** branch. This ensures that any change made is quickly reflected and tested.

  **Tool**: **Jenkins** will be used for this, and it will monitor changes in the repository using **webhooks**.
  
  **Question**: What is a code build tool, and which tool should come to mind?
  - **Answer**: When we talk about **code build**, **Jenkins** is the tool we typically use. Jenkins allows us to automate the build and deployment process.

---

#### Task 4: Deploying Changes to Production

- **Master Branch Commit**: When a commit is made to the **master** branch:
  - **Test** the changes.
  - **Push** the code to the **prod** (production) environment.
  - **Expose** the changes on the server, ensuring the new updates are visible on the browser.

  **Note**: This process should be automatic.

- **Develop Branch Commit**: When a commit is made to the **develop** branch:
  - Only build the code and do not deploy it to the production environment.
  - Do not expose it on the browser. This step is only for testing purposes.

---

#### Task 5: Containerizing the Code Using Docker

- The code should be **containerized** using **Docker**.
- **Dockerfile**: We need to create a **Dockerfile** to define how to build the container image.
- **Docker Task**:
  - Build the Docker image.
  - The image should be deployed on the **production server**.
  - The containerized code should replace the existing `index.html` file located in `/var/www/html` (typically for Apache or Nginx servers).

---

#### Task 6: Jenkins Pipeline Configuration

- We need to define a **Jenkins pipeline** with the following jobs:
  - **Job 1**: **Build** the code.
    - This job should be linked with the **master** branch.
    - The build should be done on a **test server**.
  - **Job 2**: **Test** the code.
    - This job is linked with the **develop** branch.
    - The code should be built and tested on a **test server**.
  - **Job 3**: **Finalize Deployment**.
    - Build the code, test it, and then deploy it on the **production server**.
    - Expose the deployment on the browser for users to see.

---

### Jenkins Job Details

1. **Job 1** (for Master Branch):
   - Build the code.
   - Keep the build on the **test server**.
   
2. **Job 2** (for Develop Branch):
   - Build the code.
   - Keep the build on the **test server**.
   
3. **Job 3** (for Production):
   - Build, test, and deploy the code.
   - Keep the build on the **production server**.
   - Expose the deployed application on the **browser**.

---

### Final Notes

- The **master branch** triggers the build and deployment to production.
- The **develop branch** triggers only the build and test processes.
- **Jenkins** uses webhooks to automatically detect changes in the Git repository and trigger the appropriate jobs.

---

### Conclusion

This is the overview of **Capstone Project 1**. By implementing these steps, you will be able to integrate the **DevOps lifecycle** into a product-based company, automate code deployments, and manage version control effectively.

---

Is everyone clear with the tasks and steps involved? Please let me know if you have any questions.
