# cloud-devops-two-app-pipeline
This Project design keeps the infrastructure understandable while still demonstrating the major DevOps skills the project requires.  CloudFormation manages the AWS infrastructure as a stack, while Ansible manages configuration and deployment on the EC2 host. AWS describes CloudFormation as a way to manage a collection of resources as a single unit.
# Cloud & DevOps Two-Application Deployment Pipeline.

## 📌 Project Overview

**Cloud & DevOps Two-Application Deployment Pipeline** is an end-to-end DevOps project that demonstrates how multiple applications can be containerized, provisioned, configured, tested, and deployed to AWS using modern DevOps practices.

The project deploys two independent applications:

1. **Portfolio Web Application** — a responsive web application built with HTML/CSS and served through Nginx.
2. **Java Web Application** — a Java-based application packaged as a WAR file and deployed using Apache Tomcat.

The infrastructure is provisioned on **Amazon Web Services (AWS)**, while Docker provides application containerization and Nginx provides reverse-proxy functionality.

The project also demonstrates Infrastructure as Code, configuration management, CI/CD automation, container image management, security groups, load balancing, and automated deployment.

---

# 🏗️ Architecture

The overall architecture follows a layered DevOps deployment model:

```text
                         ┌─────────────────────────┐
                         │       Developer         │
                         │                         │
                         │ Git / GitHub Repository  │
                         └────────────┬────────────┘
                                      │
                                      │ git push
                                      ▼
                         ┌─────────────────────────┐
                         │    CI/CD Pipeline       │
                         │                         │
                         │ GitHub Actions / Jenkins│
                         └────────────┬────────────┘
                                      │
                    ┌─────────────────┴─────────────────┐
                    │                                   │
                    ▼                                   ▼
          ┌──────────────────┐                ┌──────────────────┐
          │ Build & Test     │                │ Docker Build     │
          │ Applications     │                │ Container Images │
          └────────┬─────────┘                └────────┬─────────┘
                   │                                   │
                   └────────────────┬──────────────────┘
                                    │
                                    ▼
                         ┌─────────────────────────┐
                         │ GitHub Container        │
                         │ Registry (GHCR)         │
                         └────────────┬────────────┘
                                      │
                                      │ Pull approved images
                                      ▼
                         ┌─────────────────────────┐
                         │ AWS Application         │
                         │ Load Balancer           │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │ AWS EC2 Deployment Host │
                         │                         │
                         │ Docker                  │
                         │ Nginx Reverse Proxy     │
                         └────────────┬────────────┘
                                      │
                       ┌──────────────┴──────────────┐
                       │                             │
                       ▼                             ▼
              ┌─────────────────┐           ┌─────────────────┐
              │ Portfolio App   │           │ Java App        │
              │ Nginx Container │           │ Tomcat Container│
              │                 │           │                 │
              │ Port 80         │           │ Port 8080       │
              └─────────────────┘           └─────────────────┘
```

---

# 🎯 Project Objectives

The primary objectives of this project are to demonstrate practical implementation of:

* Git and GitHub version control
* Linux system administration
* Infrastructure as Code
* AWS cloud infrastructure
* EC2 provisioning
* Application Load Balancer configuration
* Docker containerization
* Multi-stage Docker builds
* Nginx reverse proxy configuration
* Java/Tomcat application deployment
* Configuration management using Ansible
* CI/CD automation
* GitHub Actions
* Jenkins pipeline automation
* GitHub Container Registry
* Secure application deployment
* Automated application updates
* Monitoring and troubleshooting
* DevOps documentation

---

# 🛠️ Technology Stack

| Category                 | Technology                    |
| ------------------------ | ----------------------------- |
| Cloud Provider           | AWS                           |
| Infrastructure as Code   | AWS CloudFormation            |
| Operating System         | Amazon Linux 2023             |
| Version Control          | Git                           |
| Source Control           | GitHub                        |
| Containerization         | Docker                        |
| Container Registry       | GitHub Container Registry     |
| Web Server               | Nginx                         |
| Reverse Proxy            | Nginx                         |
| Backend Runtime          | Java 17                       |
| Application Server       | Apache Tomcat 9               |
| Configuration Management | Ansible                       |
| CI/CD                    | GitHub Actions                |
| Automation               | Jenkins                       |
| Load Balancing           | AWS Application Load Balancer |
| Networking               | Amazon VPC                    |
| Compute                  | Amazon EC2                    |
| Security                 | AWS Security Groups           |
| Scripting                | Bash                          |
| Application Packaging    | Maven/WAR                     |

---

# 📁 Project Structure

The repository is organized to separate infrastructure, application code, automation, and deployment configuration.

```text
cloud-devops-two-app-pipeline/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── portfolio/
│   ├── index.html
│   ├── style.css
│   ├── Dockerfile
│   └── ...
│
├── java-app/
│   ├── src/
│   ├── pom.xml
│   ├── Dockerfile
│   └── ...
│
├── nginx/
│   └── nginx.conf
│
├── ansible/
│   ├── inventory
│   ├── playbook.yml
│   └── roles/
│
├── jenkins/
│   └── Jenkinsfile
│
├── infrastructure/
│   └── cloudformation.yml
│
├── docker-compose.yml
│
└── README.md
```

> The exact directory names may vary depending on the final repository structure. The purpose of each directory should remain clearly documented.

---

# 🚀 Application 1 — Portfolio Web Application

The first application is a responsive personal portfolio website.

The application contains:

* HTML
* CSS
* Responsive layout
* Portfolio/project information
* Cloud and DevOps skills
* Contact information
* Project documentation

The portfolio application is packaged into a Docker image and served using Nginx.

## Portfolio Dockerfile

The application uses an Nginx-based container:

```dockerfile
FROM nginx:alpine

COPY . /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Why Nginx?

Nginx was selected because it provides:

* High performance
* Lightweight container images
* Static file serving
* Reverse-proxy capabilities
* Easy configuration
* Production-ready architecture

---

# ☕ Application 2 — Java Application

The second application is a Java web application packaged as a WAR file.

The application uses:

* Java 17
* Maven
* Apache Tomcat 9
* Docker

The Java application is built using Maven and then deployed into Tomcat.

## Multi-Stage Dockerfile

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .

RUN mvn dependency:go-offline

COPY src ./src

RUN mvn clean package -DskipTests


FROM tomcat:9.0-jdk17-temurin

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=build /app/target/*.war \
    /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080

CMD ["catalina.sh", "run"]
```

### Multi-Stage Build Benefits

The multi-stage Docker build separates:

```text
Build Environment
       │
       │ Maven + JDK
       ▼
    WAR File
       │
       ▼
Runtime Environment
       │
       │ Tomcat + JDK
       ▼
Production Container
```

This reduces unnecessary build dependencies in the final runtime image.

---

# 🐳 Docker Containerization

Docker is used to package the applications and their runtime dependencies into portable containers.

## Build Portfolio Image

```bash
docker build -t cloud-devops-two-app-pipeline-portfolio ./portfolio
```

## Build Java Image

```bash
docker build -t cloud-devops-two-app-pipeline-java ./java-app
```

## List Docker Images

```bash
docker images
```

---

# 🔗 Docker Compose

For local development and integration testing, Docker Compose can be used to run the application stack.

Example:

```yaml
version: "3.9"

services:

  portfolio-app:
    build:
      context: ./portfolio
    container_name: portfolio-app
    restart: unless-stopped
    expose:
      - "80"

  java-app:
    build:
      context: ./java-app
    container_name: java-app
    restart: unless-stopped
    expose:
      - "8080"

  reverse-proxy:
    image: nginx:alpine
    container_name: reverse-proxy
    ports:
      - "80:80"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - portfolio-app
      - java-app
```

---

# 🌐 Nginx Reverse Proxy

Nginx is used as the entry point for the application containers.

Example configuration:

```nginx
events {}

http {

    upstream portfolio {
        server portfolio-app:80;
    }

    upstream java_app {
        server java-app:8080;
    }

    server {

        listen 80;

        location / {
            proxy_pass http://portfolio;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location /java/ {
            proxy_pass http://java_app/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

This architecture provides a single entry point while allowing multiple backend services to run independently.

---

# ☁️ AWS Infrastructure

The infrastructure is provisioned using **AWS CloudFormation**.

The infrastructure includes:

```text
AWS Region
│
├── VPC
│
├── Internet Gateway
│
├── Public Subnet
│
├── Route Table
│
├── Security Groups
│
├── EC2 Instance
│
├── Application Load Balancer
│
├── Target Group
│
└── Listener
```

---

# 🏗️ AWS CloudFormation

The CloudFormation stack automates creation of the required infrastructure.

Example stack:

```text
cloud-devops-two-app
```

The infrastructure parameters include:

```text
KeyName       = devops-key
InstanceType  = t3.micro
AMI           = Amazon Linux 2023
```

## Deploy CloudFormation Stack

```bash
aws cloudformation create-stack \
  --stack-name cloud-devops-two-app \
  --template-body file://infrastructure/cloudformation.yml \
  --parameters \
    ParameterKey=KeyName,ParameterValue=devops-key \
    ParameterKey=InstanceType,ParameterValue=t3.micro \
  --capabilities CAPABILITY_NAMED_IAM
```

## Check Stack

```bash
aws cloudformation describe-stacks \
  --stack-name cloud-devops-two-app
```

---

# 🔐 AWS Security Groups

Security groups control network access to the infrastructure.

Typical ports used by the project include:

| Port | Protocol | Purpose            |
| ---: | -------- | ------------------ |
|   22 | TCP      | SSH administration |
|   80 | TCP      | HTTP               |
|  443 | TCP      | HTTPS              |
| 8080 | TCP      | Java/Tomcat        |
|   80 | TCP      | Nginx              |

For production, application ports should preferably not be exposed directly to the public internet.

For example:

```text
Internet
   │
   ▼
ALB :80/:443
   │
   ▼
EC2 :80
   │
   ▼
Nginx
   │
   ├── Portfolio
   │
   └── Java Application :8080
```

---

# ⚖️ Application Load Balancer

The AWS Application Load Balancer provides a stable public endpoint for the application.

The ALB consists of:

* Load Balancer
* Target Group
* Listener
* Health Check

Traffic flow:

```text
Client
  │
  ▼
Application Load Balancer
  │
  ▼
Target Group
  │
  ▼
EC2 Instance
  │
  ▼
Nginx
  │
  ├── Portfolio Container
  │
  └── Java Container
```

---

# ⚙️ Ansible Configuration Management

Ansible is used to automate configuration of the EC2 deployment environment.

Typical tasks include:

1. Connect to EC2.
2. Update the operating system.
3. Install Docker.
4. Configure Docker.
5. Create application directories.
6. Copy configuration files.
7. Authenticate with the container registry.
8. Pull application images.
9. Start containers.
10. Verify application availability.

Example inventory:

```ini
[webservers]
app-server ansible_host=<EC2_PUBLIC_IP>

[webservers:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/devops-key.pem
```

---

# 📜 Example Ansible Playbook

```yaml
---
- name: Configure application server
  hosts: webservers
  become: true

  tasks:

    - name: Install Docker
      ansible.builtin.dnf:
        name: docker
        state: present

    - name: Start Docker
      ansible.builtin.service:
        name: docker
        state: started
        enabled: true

    - name: Add ec2-user to Docker group
      ansible.builtin.user:
        name: ec2-user
        groups: docker
        append: true

    - name: Create application directory
      ansible.builtin.file:
        path: /opt/cloud-devops-app
        state: directory
        mode: "0755"
```

---

# 🔄 CI/CD Pipeline

The project implements CI/CD automation so that application changes can be automatically validated, built, packaged, and deployed.

The pipeline follows:

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Checkout
    │
    ├── Validate
    │
    ├── Build
    │
    ├── Test
    │
    ├── Docker Build
    │
    ├── Docker Login
    │
    ├── Push Image
    │
    └── Deploy
             │
             ▼
       AWS EC2 Host
             │
             ▼
        Docker Runtime
```

---

# 🔧 GitHub Actions

The GitHub Actions workflow is stored under:

```text
.github/workflows/ci.yml
```

Typical pipeline stages include:

### 1. Checkout

```yaml
- name: Checkout repository
  uses: actions/checkout@v4
```

### 2. Validate

```yaml
- name: Validate Portfolio
  run: |
    test -f portfolio/index.html
    test -f portfolio/Dockerfile
```

### 3. Build Docker Image

```bash
docker build \
  -t ghcr.io/georgelolu/cloud-devops-two-app-pipeline-portfolio:latest \
  ./portfolio
```

### 4. Authenticate with GHCR

```yaml
- name: Log in to GHCR
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```

### 5. Push Image

```bash
docker push ghcr.io/georgelolu/cloud-devops-two-app-pipeline-portfolio:latest
```

---

# 📦 GitHub Container Registry

GitHub Container Registry (GHCR) is used to store the Docker images produced by the CI/CD pipeline.

Example:

```text
ghcr.io/georgelolu/
```

Images:

```text
cloud-devops-two-app-pipeline-portfolio
cloud-devops-two-app-pipeline-java
```

The general workflow is:

```text
Source Code
     │
     ▼
GitHub Actions
     │
     ▼
Docker Build
     │
     ▼
GHCR
     │
     ▼
AWS EC2
     │
     ▼
Docker Pull
     │
     ▼
Application Container
```

---

# 🔑 GitHub Secrets

Sensitive credentials should never be committed to GitHub.

The pipeline can use GitHub repository secrets such as:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
EC2_HOST
EC2_USERNAME
EC2_SSH_KEY
GHCR_TOKEN
```

Secrets should be configured under:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
```

---

# 🔨 Jenkins Automation

Jenkins can also be used as an alternative CI/CD orchestration platform.

A typical Jenkins pipeline follows:

```text
Checkout
   │
   ▼
Build
   │
   ▼
Test
   │
   ▼
Docker Build
   │
   ▼
Push to Registry
   │
   ▼
Deploy
   │
   ▼
Health Check
```

Example Jenkinsfile:

```groovy
pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t cloud-devops-two-app ./portfolio'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Running application tests..."'
            }
        }

        stage('Deploy') {
            steps {
                sh 'echo "Deploying application..."'
            }
        }
    }
}
```

---

# 🔁 Complete Deployment Workflow

The complete workflow is:

```text
1. Developer modifies source code
             │
             ▼
2. Git commit
             │
             ▼
3. Git push
             │
             ▼
4. GitHub repository
             │
             ▼
5. CI/CD pipeline starts
             │
             ▼
6. Source validation
             │
             ▼
7. Application build
             │
             ▼
8. Automated tests
             │
             ▼
9. Docker image creation
             │
             ▼
10. Image pushed to GHCR
             │
             ▼
11. Deployment server pulls image
             │
             ▼
12. Existing container stopped
             │
             ▼
13. New container started
             │
             ▼
14. Health check
             │
             ▼
15. Application available
```

---

# 🧪 Local Testing

Before deploying to AWS, the applications can be tested locally.

## Check Docker

```bash
docker --version
```

```bash
docker compose version
```

## Build Containers

```bash
docker compose build
```

## Start Services

```bash
docker compose up -d
```

## Check Running Containers

```bash
docker ps
```

## View Logs

```bash
docker logs portfolio-app
```

```bash
docker logs java-app
```

```bash
docker logs reverse-proxy
```

## Stop Services

```bash
docker compose down
```

---

# 🩺 Application Health Checks

The deployment should verify that services are running after deployment.

Example:

```bash
curl http://localhost
```

For the Java application:

```bash
curl http://localhost:8080
```

Check container health:

```bash
docker ps
```

Check listening ports:

```bash
ss -lntp
```

---

# 🔍 Troubleshooting

## Docker Port Already in Use

If port `80` is already occupied:

```bash
sudo ss -lntp | grep :80
```

Stop the process or change the host port mapping.

For example:

```yaml
ports:
  - "8081:80"
```

---

## Docker Container Name Conflict

Check existing containers:

```bash
docker ps -a
```

Remove the conflicting container:

```bash
docker rm -f <container-name>
```

Then restart:

```bash
docker compose up -d
```

---

## Dockerfile Not Found

Verify the project structure:

```bash
ls -la
```

```bash
ls -la portfolio
```

Make sure the Dockerfile exists:

```bash
portfolio/Dockerfile
```

Then build with the correct context:

```bash
docker build -t portfolio-app ./portfolio
```

---

## GitHub Container Registry Image Name Error

Docker image names must be lowercase.

Correct:

```text
ghcr.io/georgelolu/cloud-devops-two-app-pipeline-portfolio
```

Avoid uppercase repository/image names.

---

## GitHub Actions SSH Timeout

If GitHub Actions cannot connect to EC2 over SSH:

Check:

```text
EC2 public IP
Security Group
Port 22
SSH private key
EC2 username
Network ACL
```

Verify the security group permits SSH from the appropriate source.

For better security, avoid opening SSH to:

```text
0.0.0.0/0
```

unless temporarily required for testing.

---

# 🔒 Security Considerations

Security was considered throughout the deployment architecture.

Important practices include:

### 1. Protect SSH

Restrict port 22 to trusted IP addresses.

### 2. Protect Secrets

Never commit:

```text
.pem
.env
AWS credentials
GitHub tokens
private keys
passwords
```

Add sensitive files to `.gitignore`.

Example:

```gitignore
.env
*.pem
*.key
.aws/
```

### 3. Use IAM

AWS resources should use IAM roles and least-privilege permissions where possible.

### 4. Container Security

Use:

* Minimal base images
* Multi-stage builds
* Regular image updates
* Non-root containers where supported
* Vulnerability scanning

### 5. Network Segmentation

Only expose required ports.

Preferred architecture:

```text
Internet
   │
   ▼
ALB
   │
   ▼
EC2
   │
   ▼
Nginx
   │
   ├── Portfolio
   └── Java
```

---

# 📊 DevOps Practices Demonstrated

This project demonstrates the following DevOps principles:

| Practice                 | Implementation                       |
| ------------------------ | ------------------------------------ |
| Version Control          | Git/GitHub                           |
| Infrastructure as Code   | CloudFormation                       |
| Configuration Management | Ansible                              |
| Containerization         | Docker                               |
| Image Management         | GHCR                                 |
| CI/CD                    | GitHub Actions                       |
| Automation               | Jenkins                              |
| Reverse Proxy            | Nginx                                |
| Load Balancing           | AWS ALB                              |
| Cloud Computing          | AWS                                  |
| Application Packaging    | Maven/WAR                            |
| Security                 | AWS Security Groups + Secrets        |
| Testing                  | Local Docker testing + health checks |
| Documentation            | GitHub README                        |

---

# 📈 CI/CD Benefits

The pipeline reduces manual deployment work.

Without CI/CD:

```text
Developer
   │
   ▼
Manual Build
   │
   ▼
Manual Docker Build
   │
   ▼
Manual Image Push
   │
   ▼
Manual SSH
   │
   ▼
Manual Deployment
```

With CI/CD:

```text
Developer
   │
   ▼
git push
   │
   ▼
Automated Pipeline
   │
   ├── Build
   ├── Test
   ├── Package
   ├── Docker Build
   ├── Registry Push
   └── Deployment
```

This improves:

* Deployment speed
* Repeatability
* Reliability
* Traceability
* Developer productivity
* Operational consistency

---

# 📸 Project Screenshots

Screenshots should be added to demonstrate successful implementation.

Recommended screenshots include:

### AWS Infrastructure

```text
docs/screenshots/aws/
├── cloudformation-stack.png
├── ec2-instance.png
├── load-balancer.png
├── target-group.png
└── security-groups.png
```

### Docker

```text
docs/screenshots/docker/
├── docker-images.png
├── docker-containers.png
└── docker-compose.png
```

### GitHub Actions

```text
docs/screenshots/github-actions/
├── workflow-success.png
├── docker-build.png
└── deployment-success.png
```

### GHCR

```text
docs/screenshots/ghcr/
├── portfolio-image.png
└── java-image.png
```

### Applications

```text
docs/screenshots/application/
├── portfolio.png
├── java-application.png
└── load-balancer.png
```

---

# 🧾 Example Git Workflow

Create a feature branch:

```bash
git checkout -b feature/application-update
```

Make changes:

```bash
git add .
```

Commit:

```bash
git commit -m "Update application deployment configuration"
```

Push:

```bash
git push origin feature/application-update
```

Create a pull request on GitHub.

After review and validation, merge into the main branch.

---

# 🧹 Cleanup

To stop local containers:

```bash
docker compose down
```

Remove unused containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

To remove the AWS CloudFormation stack:

```bash
aws cloudformation delete-stack \
  --stack-name cloud-devops-two-app
```

Verify deletion:

```bash
aws cloudformation describe-stacks \
  --stack-name cloud-devops-two-app
```

---

# 💰 AWS Cost Considerations

This project uses AWS resources that may incur charges depending on the AWS account and resource configuration.

Potential billable resources include:

* EC2
* Application Load Balancer
* Elastic IP
* EBS storage
* Data transfer

For development environments:

* Stop unused EC2 instances.
* Delete unused load balancers.
* Remove unused EBS volumes.
* Delete unused Elastic IPs.
* Delete CloudFormation stacks when the project is complete.

Always verify the current AWS pricing and Free Tier eligibility before deploying.

---

# 🚀 Future Improvements

The current implementation can be extended with additional production-grade DevOps practices.

## 1. SonarQube

Introduce static code analysis:

```text
GitHub
   │
   ▼
GitHub Actions
   │
   ▼
SonarQube
   │
   ▼
Quality Gate
   │
   ▼
Docker Build
```

## 2. HTTPS

Add:

* AWS Certificate Manager
* HTTPS listener
* HTTP → HTTPS redirect

## 3. Terraform

Replace or complement CloudFormation with Terraform.

## 4. Kubernetes

Move from standalone Docker containers to:

```text
EKS
```

## 5. Monitoring

Introduce:

* Prometheus
* Grafana
* CloudWatch
* Loki

## 6. Vulnerability Scanning

Integrate:

* Trivy
* Docker Scout
* Dependabot

## 7. Blue/Green Deployment

Introduce zero-downtime deployment using separate application environments.

## 8. Auto Scaling

Use:

```text
ALB
+
Auto Scaling Group
+
EC2
```

to improve application availability.

---

# 🏆 Project Outcomes

By completing this project, the following practical DevOps capabilities were demonstrated:

* Designed a complete cloud deployment architecture.
* Provisioned AWS infrastructure using Infrastructure as Code.
* Configured EC2-based application hosting.
* Containerized multiple applications using Docker.
* Created a multi-stage Java/Tomcat Docker image.
* Served a web application using Nginx.
* Implemented reverse-proxy routing.
* Configured an AWS Application Load Balancer.
* Automated server configuration with Ansible.
* Implemented CI/CD using GitHub Actions.
* Integrated Docker images with GitHub Container Registry.
* Automated application deployment.
* Used Jenkins for pipeline automation.
* Troubleshot Docker, networking, SSH, and deployment issues.
* Applied DevOps security practices.
* Documented the entire deployment process.

---

# 📚 Key DevOps Concepts Learned

This project provided practical experience with:

### Infrastructure as Code

Infrastructure should be reproducible rather than manually created.

### Immutable Infrastructure

Applications are deployed using versioned container images rather than manually modifying servers.

### Continuous Integration

Every code change can be automatically built and validated.

### Continuous Deployment

Validated changes can automatically progress toward deployment.

### Configuration Management

Ansible provides repeatable server configuration.

### Containerization

Docker packages applications and dependencies into portable units.

### Registry Management

GHCR provides centralized storage for container images.

### Load Balancing

The ALB provides a scalable entry point for application traffic.

### Automation

The objective is to reduce repetitive manual operational tasks.

---

# 🧑‍💻 Author

**George Omololu Akinbi**

Cloud & DevOps Engineer

### GitHub

https://github.com/georgelolu

### LinkedIn

https://linkedin.com/in/georgelolu

### Email

[georgelolu@gmail.com](mailto:georgelolu@gmail.com)

---

# 📄 License

This project is licensed under the MIT License.

You are free to use, modify, and distribute the project for learning and development purposes.

---

# ⭐ Acknowledgements

This project was developed as a practical Cloud & DevOps engineering project to demonstrate real-world implementation of:

* AWS
* Linux
* Git/GitHub
* Docker
* Ansible
* Jenkins
* GitHub Actions
* Nginx
* Maven
* Tomcat
* CI/CD
* Infrastructure as Code

---

# ⭐ If You Found This Project Useful

Consider giving the repository a ⭐ on GitHub and exploring the implementation.

```text
git clone https://github.com/georgelolu/cloud-devops-two-app-pipeline.git

cd cloud-devops-two-app-pipeline
```

---

## 📌 Final Architecture Summary

```text
                         ┌──────────────────┐
                         │     Developer    │
                         └────────┬─────────┘
                                  │
                              git push
                                  │
                                  ▼
                         ┌──────────────────┐
                         │     GitHub       │
                         └────────┬─────────┘
                                  │
                                  ▼
                     ┌────────────────────────┐
                     │    CI/CD Pipeline      │
                     │                        │
                     │ GitHub Actions/Jenkins │
                     └───────────┬────────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                    ▼                         ▼
              Build & Test              Docker Build
                    │                         │
                    └────────────┬────────────┘
                                 ▼
                         ┌─────────────────┐
                         │      GHCR       │
                         │ Docker Registry │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   AWS ALB       │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │    AWS EC2      │
                         │                 │
                         │ Docker          │
                         │ Nginx           │
                         └────────┬────────┘
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
             Portfolio App                Java App
             Nginx Container              Tomcat Container
             Port 80                      Port 8080
```

**Project Status: Completed and Successfully Deployed**

This project demonstrates a complete practical DevOps lifecycle from **source code → infrastructure → containerization → CI/CD → container registry → AWS deployment → application delivery**.


