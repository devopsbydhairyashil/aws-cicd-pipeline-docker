# aws-cicd-pipeline-docker
End-to-end CI/CD pipeline using AWS services (CodeCommit, CodeBuild, CodeDeploy, and CodePipeline) integrated with Docker for automated application build, test, and deployment.

# Automated CI/CD Pipeline using AWS CodePipeline, CodeBuild & Docker

This project demonstrates a *fully automated CI/CD pipeline* on AWS for containerized application deployment.  
It integrates *AWS CodePipeline, **CodeBuild, **ECR, and **ECS* to automate build, test, and deployment stages whenever new code is pushed to GitHub.

---

## Project Overview
The goal of this project is to implement a *DevOps pipeline* that:
- Automatically builds and tests source code after each commit.
- Builds a *Docker image* and pushes it to *Amazon ECR*.
- Deploys the latest container to *Amazon ECS*.
- Implements version control and rollback capabilities.

---

## Architecture Diagram

GitHub → CodePipeline → CodeBuild → ECR → ECS (Fargate)

---

##  AWS Services Used
- *AWS CodePipeline* – Manages CI/CD workflow.  
- *AWS CodeBuild* – Builds and tests source code.  
- *Amazon ECR* – Stores built Docker images.  
- *Amazon ECS (Fargate)* – Deploys and runs containers.  
- *IAM* – Handles access permissions.  

---

## Tools & Technologies
- *Docker*  
- *AWS CLI*  
- *Git & GitHub*  
- *YAML configuration for pipeline*  
- *Linux / Bash scripting*

---

## Project Structure

aws-cicd-pipeline-docker/
├── buildspec.yml              # Build configuration for CodeBuild
├── Dockerfile                 # Docker image setup
├── app/                       # Application source code
├── scripts/                   # Deployment scripts
└── README.md                  # Project documentation

---

##  Setup Instructions

### 1️ Clone the repository
```bash
git clone https://github.com/<your-username>/aws-cicd-pipeline-docker.git
cd aws-cicd-pipeline-docker

2️ Create an ECR Repository

aws ecr create-repository --repository-name devops-demo --region ap-south-1

3️ Build and Push Docker Image

docker build -t devops-demo .
docker tag devops-demo:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/devops-demo:latest
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/devops-demo:latest

4️ Configure CodePipeline
	1.	Go to AWS CodePipeline.
	2.	Create a new pipeline and connect to your GitHub repo.
	3.	Add CodeBuild as build stage → use buildspec.yml.
	4.	Add ECS deploy stage → choose your ECS cluster and service.

⸻

 Sample buildspec.yml

version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on date
      - echo Building the Docker image...
      - docker build -t devops-demo .
      - docker tag devops-demo:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/devops-demo:latest
  post_build:
    commands:
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/devops-demo:latest
      - echo Build completed on date
artifacts:
  files:
    - '/*'


⸻

 Deployment

Once CodePipeline is configured, every new push to the main branch automatically:
	•	Builds a Docker image
	•	Pushes it to ECR
	•	Updates the ECS service with the new image

⸻

 Results

✅ Fully automated deployment
✅ No manual image builds
✅ Version-controlled workflow
✅ Real-world AWS DevOps CI/CD setup

⸻

Author

Dhairyashil – Cloud & DevOps Engineer
 Connect on LinkedIn
https://www.linkedin.com/in/dhairyashilclouddevops/
⸻

🏷 Topics

aws cicd docker codepipeline devops cloud automation

