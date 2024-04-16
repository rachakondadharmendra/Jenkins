# Secure CI/CD Pipeline: DevSecOps Workflow

![DevSecOps_gif](https://github.com/rachakondadharmendra/Ops-Knowledge-Base/blob/main/Arch-Daigrams/Jenkins_DevSecOps_CI_CD.gif)

This repository contains the setup for Continuous Integration (CI) and Continuous Deployment (CD) processes using Jenkins and Argo CD respectively. Below are the details of the tools and processes involved:

## CI Part

### Version Control Tool
- **Tool:** Git
- **Purpose:** Git is used for version control to manage the source code of the project.

### Secret Scanning Tool
- **Tools:** GitLeaks or Git Secrets
- **Purpose:** Used for scanning code repositories for sensitive information such as passwords, API keys, and tokens.

### Source Composition Analysis (SCA)
- **Tool:** OWASP Dependency-Check
- **Purpose:** Dependency-Check is utilized for identifying known vulnerabilities within project dependencies.

### Static Application Security Testing (SAST)
- **Tools:**
  - ESLint
  - SonarQube
  - Checkov
- **Purpose:** These tools are employed for static code analysis to identify potential security vulnerabilities, coding errors, and adherence to best practices.

### Dynamic Application Security Testing (DAST)
- **Tool:** Smoke Tests
- **Purpose:** Smoke tests are executed to perform dynamic security testing and ensure the application behaves correctly under various conditions.

### Containerization Tools
- **Tool:** Trivy
- **Purpose:** Trivy is utilized for scanning container images for vulnerabilities and exposures.

### Images Hub
- **Hub:** ECR, Docker Registry
- **Purpose:** ECR (Amazon Elastic Container Registry) and Docker Registry are used for storing and managing container images.

## CD Part

### Using Argo CD (Kubernetes Cluster Deployment)
- **Tool:** Argo CD
- **Purpose:** Argo CD automates the deployment of applications in Kubernetes clusters, ensuring consistency and reliability.

### Normal Server Image Pull and Deployment into K8 Cluster
- **Purpose:** Pulls the latest version of the server image and deploys it into the Kubernetes cluster using Argo CD.

### Remote Server Docker Compose Pull
- **Purpose:** Pulls Docker Compose configurations for remote server deployment.
