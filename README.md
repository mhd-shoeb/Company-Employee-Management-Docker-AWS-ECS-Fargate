# Dockerized Employee Management Application on AWS ECS Fargate

## Project Overview

This project demonstrates how to containerize a Python Flask web application using Docker, store the container image in Amazon Elastic Container Registry (ECR), and deploy it using Amazon Elastic Container Service (ECS) with AWS Fargate.

The application displays a Company Employee Management landing page and provides a health-check endpoint.

## Architecture

```text
Developer
   |
   v
Python Flask Application
   |
   v
Docker Image
   |
   v
Amazon ECR
   |
   v
Amazon ECS
   |
   v
AWS Fargate
   |
   v
Running Container
   |
   v
Web Browser
```

## Technologies Used

- Linux
- Python 3.12
- Flask
- Docker
- Amazon ECR
- Amazon ECS
- AWS Fargate
- AWS IAM
- Amazon VPC
- Security Groups

## Project Structure

```text
company-ecs-project/
├── app.py
├── requirements.txt
├── Dockerfile
└── templates/
    └── index.html
```

## Application Features

- Flask-based web application.
- Docker containerization.
- Health-check endpoint at `/health`.
- Container image stored in Amazon ECR.
- Application deployed using ECS Fargate.
- Public access through the ECS task's public IP.

## Deployment Steps

### 1. Build Docker Image

```bash
docker build -t company-pp:v1 .
(note: . will use current directory as build context)
```

### 2. Run Container Locally

```bash
docker run -d -p 5000:5000 company-pp:v1
```

### 3. Verify Container

```bash
docker images
docker ps -a
container exited state then execute - docker start "container name"
```

Open:

```text
http://localhost:5000
```

### 4. Push Image to Amazon ECR

Create an ECR repository, authenticate Docker with ECR, tag the image and push it.

Example:

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

docker tag company-app:v1 <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/company-pp:v2

docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/company-pp:v2
```

### 5. Create ECS Cluster

- Cluster name: company-cluster
- Infrastructure: AWS Fargate

### 6. Create Task Definition

- Task family: company-task
- Launch type: Fargate
- Container image: Amazon ECR image
- Container port: 5000
- Operating system: Linux

### 7. Create ECS Service

- Service name: company-service
- Cluster: company-cluster
- Compute: Fargate
- Networking: VPC and public subnet
- Assign public IP: Enabled
- Security group: Allow TCP 5000 from authorized client IP

### 8. Verify Deployment

Access the application:

```text
http://<PUBLIC-IP>:5000
```

Health check:

```text
http://<PUBLIC-IP>:5000/health
```
```text
goto ECS => cluster => select services => open company-service => open task tab => click running ID => find networking section look for public IP.
```

## Expected Output

```text
Welcome to Company Employee Management

Application Successfully Deployed!

Running inside a Docker container.
```

Health endpoint:

```json
{
  "status": "healthy",
  "application": "Company Employee Management"
}
```

## Key Learnings

1. Building and managing Docker images.
2. Containerizing Python applications.
3. Publishing Docker images to Amazon ECR.
4. Creating ECS clusters and task definitions.
5. Deploying containerized applications using AWS Fargate.
6. Configuring IAM execution permissions.
7. Configuring VPC networking and security groups.
8. Accessing and validating a deployed cloud application.

**Note:** This is a learning project demonstrating Docker containerization and AWS ECS Fargate deployment.
