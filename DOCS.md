# Complete DevOps Guide: Git, Docker & Jenkins

This comprehensive guide explains the core concepts of Git, Docker, and Jenkins with practical examples from this Django Todo project.

---

## Table of Contents

1. [Git - Version Control System](#1-git---version-control-system)
2. [Docker - Container Platform](#2-docker---container-platform)
3. [Jenkins - CI/CD Automation](#3-jenkins---cicd-automation)
4. [How They Work Together](#4-how-they-work-together)
5. [Project-Specific Commands](#5-project-specific-commands)

---

## 1. Git - Version Control System

### What is Git?

Git is a **distributed version control system** that tracks changes in your source code during software development. It allows multiple developers to work together on the same project.

### Key Concepts

| Term | Description |
|------|-------------|
| **Repository (Repo)** | A folder where Git stores your project files and history |
| **Commit** | A saved change in your Git history |
| **Branch** | A parallel version of your code |
| **Push** | Upload local commits to a remote repository |
| **Pull** | Download changes from remote repository |
| **Clone** | Create a local copy of a remote repository |
| **Merge** | Combine changes from different branches |

### Git Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    WORKING DIRECTORY                     │
│  (Your project files - where you edit code)              │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼ (git add)
┌─────────────────────────────────────────────────────────┐
│                      STAGING AREA                        │
│  (Files prepared for commit)                             │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼ (git commit)
┌─────────────────────────────────────────────────────────┐
│                    LOCAL REPOSITORY                      │
│  (.git folder - stores all commits)                      │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼ (git push)
┌─────────────────────────────────────────────────────────┐
│                    REMOTE REPOSITORY                     │
│  (GitHub/GitLab - stores code in the cloud)              │
└─────────────────────────────────────────────────────────┘
```

### Common Git Commands

```bash
# Initialize a new Git repository
git init

# Clone a remote repository
git clone https://github.com/username/repo.git

# Check status of files
git status

# Stage files for commit
git add <filename>
git add .  # Add all files

# Commit staged changes
git commit -m "Description of changes"

# Push changes to remote
git push origin main

# Pull changes from remote
git pull origin main

# Create a new branch
git branch feature-name

# Switch to a branch
git checkout feature-name

# Merge branches
git merge feature-name
```

### Git Workflow in This Project

```bash
# 1. Make changes to your code
# Edit files in your project

# 2. Check what changed
git status
git diff

# 3. Stage your changes
git add Jenkinsfile todo/todo/settings.py

# 4. Commit with a meaningful message
git commit -m "Fix Jenkins Docker agent and Django static files"

# 5. Push to GitHub
git push origin main

# 6. Jenkins automatically detects the push and deploys!
```

### Branching Strategy

```
main (Production)
  │
  ├─── feature/login-system
  │      └─── Developer works on login feature
  │
  ├─── feature/docker-setup
  │      └─── Developer adds Dockerfile
  │
  └─── bugfix/security-patch
         └─── Fix security issues
```

---

## 2. Docker - Container Platform

### What is Docker?

Docker is a **containerization platform** that packages applications with all their dependencies into standardized units called containers. Containers are lightweight, portable, and run consistently across different environments.

### Why Docker?

**Problem:** "It works on my machine!" 😓

**Solution:** Docker ensures your app runs the same everywhere:
- Your laptop
- Testing server
- Production server
- Cloud (AWS, Azure, GCP)

### Key Concepts

| Term | Description |
|------|-------------|
| **Image** | A blueprint/template for containers (like a class) |
| **Container** | A running instance of an image (like an object) |
| **Dockerfile** | A text file with instructions to build an image |
| **Docker Hub** | A registry of public Docker images |
| **Volume** | Persistent storage for containers |
| **Port** | Network endpoint for container communication |

### Docker Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    DOCKER HOST                           │
│  ┌─────────────────────────────────────────────────┐    │
│  │              Docker Daemon                       │    │
│  │  (Background service that manages containers)   │    │
│  └─────────────────────────────────────────────────┘    │
│                          │                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │Container │  │Container │  │Container │              │
│  │   App 1  │  │   App 2  │  │   App 3  │              │
│  └──────────┘  └──────────┘  └──────────┘              │
│       │              │              │                   │
│  ┌────┴──────┐  ┌────┴──────┐  ┌────┴──────┐          │
│  │  Image 1  │  │  Image 2  │  │  Image 3  │          │
│  └───────────┘  └───────────┘  └───────────┘          │
└─────────────────────────────────────────────────────────┘
```

### Dockerfile Explained

Our project's Dockerfile:

```dockerfile
# Start with official Python image
FROM python:3.12-slim

# Set working directory inside container
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy all project files
COPY . .

# Expose port 8000
EXPOSE 8000

# Run Django development server
CMD ["python", "todo/manage.py", "runserver", "0.0.0.0:8000"]
```

### How Docker Build Works

```
┌─────────────────────────────────────────────────────────┐
│  Step 1: FROM python:3.12-slim                          │
│  → Downloads official Python base image                 │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 2: WORKDIR /app                                   │
│  → Creates /app directory and sets as working directory │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 3: COPY requirements.txt .                        │
│  → Copies requirements.txt into /app                    │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 4: RUN pip install...                             │
│  → Installs all Python dependencies                     │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 5: COPY . .                                       │
│  → Copies all project files into container              │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 6: EXPOSE 8000                                    │
│  → Opens port 8000 for network traffic                  │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  Step 7: CMD [...]                                      │
│  → Sets the command to run when container starts        │
└─────────────────────────────────────────────────────────┘
```

### Common Docker Commands

```bash
# Build an image from Dockerfile
docker build -t django-todo .

# List all images
docker images

# Run a container
docker run -d -p 8000:8000 --name django-todo django-todo

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# View container logs
docker logs django-todo

# Stop a container
docker stop django-todo

# Remove a container
docker rm django-todo

# Remove an image
docker rmi django-todo

# Execute command inside running container
docker exec -it django-todo bash

# Build and run in one command
docker-compose up --build
```

### Docker Networking

```
┌─────────────────────────────────────────────────────────┐
│                    YOUR COMPUTER                         │
│                                                          │
│  ┌─────────────────┐         ┌─────────────────┐       │
│  │   Host Machine  │         │   Docker        │       │
│  │   (Port 8000)   │◄───────►│   Container     │       │
│  │                 │  Port   │   (Port 8000)   │       │
│  └─────────────────┘  Mapping └─────────────────┘       │
│                                                          │
│  Browser: localhost:8000  →  Container: 0.0.0.0:8000    │
└─────────────────────────────────────────────────────────┘
```

### Docker Volumes

```bash
# Mount host directory to container
docker run -v /host/path:/container/path image-name

# Example: Persist database data
docker run -v db-data:/var/lib/postgresql/data postgres
```

---

## 3. Jenkins - CI/CD Automation

### What is Jenkins?

Jenkins is an **open-source automation server** that automates the software development process. It implements **Continuous Integration (CI)** and **Continuous Deployment (CD)**.

### CI/CD Explained

**Continuous Integration (CI):**
- Developers frequently merge code changes
- Automated builds and tests run on each commit
- Catches bugs early

**Continuous Deployment (CD):**
- Automatically deploys tested code to production
- Reduces manual intervention
- Faster release cycles

### CI/CD Pipeline Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    CI/CD PIPELINE                                │
│                                                                  │
│  Code → Build → Test → Package → Deploy → Monitor               │
│   │       │       │        │        │         │                 │
│   ▼       ▼       ▼        ▼        ▼         ▼                 │
│  Git   Docker  Jest    Docker   Jenkins  Prometheus            │
│        Image   Tests   Image    Deploy   Monitoring            │
└─────────────────────────────────────────────────────────────────┘
```

### Jenkins Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    JENKINS MASTER                        │
│  (Main server - manages builds, schedules jobs)         │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Web Interface (Port 8080)                       │  │
│  │  - Configure jobs                                │  │
│  │  - View build history                           │  │
│  │  - Monitor status                               │  │
│  └──────────────────────────────────────────────────┘  │
│                          │                               │
│         ┌────────────────┼────────────────┐            │
│         ▼                ▼                ▼            │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐        │
│  │  Agent 1 │    │  Agent 2 │    │  Agent 3 │        │
│  │ (Linux)  │    │ (Windows)│    │ (Docker) │        │
│  └──────────┘    └──────────┘    └──────────┘        │
│       │               │               │               │
│   Build          Build           Build               │
│   Projects       Projects        Projects            │
└─────────────────────────────────────────────────────────┘
```

### Jenkinsfile Explained

Our project's Jenkinsfile:

```groovy
pipeline {
    // Run on any available agent
    agent any

    stages {
        // Stage 1: Get code from Git
        stage('Checkout') {
            steps {
                checkout scm  // SCM = Source Code Management
            }
        }

        // Stage 2: Build Docker image
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t django-todo .'
            }
        }

        // Stage 3: Run Docker container
        stage('Run Container') {
            steps {
                sh '''
                docker stop django-todo || true
                docker rm django-todo || true
                docker run -d -p 8000:8000 --name django-todo django-todo
                '''
            }
        }

        // Stage 4: Verify deployment
        stage('Health Check') {
            steps {
                sh '''
                sleep 5
                curl -f http://localhost:8000/loginn/ || exit 1
                '''
            }
        }
    }

    // Post-deployment actions
    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed! Check logs for details.'
        }
    }
}
```

### Jenkins Pipeline Stages

```
┌─────────────────────────────────────────────────────────┐
│  STAGE 1: Checkout                                       │
│  ┌─────────────────────────────────────────────────┐    │
│  │  • Pulls latest code from GitHub                │    │
│  │  • Gets Jenkinsfile                             │    │
│  │  • Prepares workspace                          │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  STAGE 2: Build Docker Image                             │
│  ┌─────────────────────────────────────────────────┐    │
│  │  • Reads Dockerfile                            │    │
│  │  • Installs dependencies                       │    │
│  │  • Creates django-todo image                   │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  STAGE 3: Run Container                                  │
│  ┌─────────────────────────────────────────────────┐    │
│  │  • Stops old container (if running)             │    │
│  │  • Removes old container                        │    │
│  │  • Starts new container on port 8000            │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│  STAGE 4: Health Check                                   │
│  ┌─────────────────────────────────────────────────┐    │
│  │  • Waits 5 seconds for app to start             │    │
│  │  • Tests login page with curl                   │    │
│  │  • Fails pipeline if page doesn't load          │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### Common Jenkins Commands

```bash
# Start Jenkins (Windows)
java -jar jenkins.war

# Start Jenkins in Docker
docker run -d -p 8080:8080 -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  --name jenkins \
  jenkins/jenkins:lts

# Get Jenkins admin password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword

# Restart Jenkins
docker restart jenkins

# View Jenkins logs
docker logs jenkins
```

### Jenkins Setup Steps

1. **Install Jenkins:**
   ```bash
   docker run -d -p 8080:8080 -p 50000:50000 \
     --name jenkins \
     jenkins/jenkins:lts
   ```

2. **Access Jenkins:**
   - Open browser: `http://localhost:8080`
   - Enter admin password

3. **Install Plugins:**
   - Go to Manage Jenkins → Plugins
   - Install: Git, Docker, Pipeline, GitHub Integration

4. **Create Pipeline:**
   - New Item → Enter name → Select "Pipeline"
   - Pipeline Definition: "Pipeline script from SCM"
   - SCM: Git
   - Repository URL: Your GitHub repo
   - Script Path: `Jenkinsfile`

5. **Configure Webhook (GitHub):**
   - GitHub Repo → Settings → Webhooks
   - Add webhook: `http://your-jenkins:8080/github-webhook/`

---

## 4. How They Work Together

### Complete DevOps Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    COMPLETE DEVOPS FLOW                          │
│                                                                  │
│  ┌──────────┐                                                   │
│  │ Developer│                                                   │
│  └────┬─────┘                                                   │
│       │ 1. Writes code                                          │
│       ▼                                                         │
│  ┌──────────┐                                                   │
│  │   Git    │ 2. Commits & Pushes to GitHub                    │
│  └────┬─────┘                                                   │
│       │                                                         │
│       │ 3. GitHub Webhook triggers                              │
│       ▼                                                         │
│  ┌──────────┐                                                   │
│  │ Jenkins  │ 4. Detects changes                               │
│  └────┬─────┘                                                   │
│       │ 5. Reads Jenkinsfile                                   │
│       ▼                                                         │
│  ┌──────────┐                                                   │
│  │  Docker  │ 6. Builds new image                              │
│  └────┬─────┘                                                   │
│       │ 7. Runs new container                                  │
│       ▼                                                         │
│  ┌──────────┐                                                   │
│  │   App    │ 8. Deployed and running!                         │
│  └──────────┘                                                   │
└─────────────────────────────────────────────────────────────────┘
```

### Step-by-Step Example

```bash
# Step 1: Developer makes changes
# Edit todo/todo/views.py

# Step 2: Commit and push
git add todo/todo/views.py
git commit -m "Add new feature"
git push origin main

# Step 3: GitHub sends webhook to Jenkins
# (Automatic - no command needed)

# Step 4: Jenkins executes pipeline
# - Checkout: git pull
# - Build: docker build -t django-todo .
# - Deploy: docker run -d -p 8000:8000 django-todo
# - Test: curl http://localhost:8000/loginn/

# Step 5: Application updated!
# Users access: http://localhost:8000
```

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        DEVELOPER                                 │
│                    (Makes code changes)                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ git push
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      GitHub Repository                           │
│         github.com/ankursingh1408/django-todo-devops            │
│                                                                  │
│  Files:                                                         │
│  - Jenkinsfile (Pipeline definition)                            │
│  - Dockerfile (Container definition)                            │
│  - todo/ (Django application)                                   │
│  - requirements.txt (Python dependencies)                       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Webhook (HTTP POST)
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Jenkins Server                               │
│                  (Running on port 8080)                         │
│                                                                  │
│  Pipeline Stages:                                               │
│  1. Checkout → git clone                                        │
│  2. Build → docker build                                        │
│  3. Deploy → docker run                                         │
│  4. Test → curl health check                                    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Docker commands
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Docker Daemon                                │
│                                                                  │
│  Images:                                                        │
│  - django-todo:latest                                           │
│  - python:3.12-slim                                             │
│  - jenkins/jenkins:lts                                          │
│                                                                  │
│  Containers:                                                    │
│  - django-todo (Port 8000)                                      │
│  - jenkins (Port 8080)                                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ HTTP Request
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      End Users                                  │
│              Access: http://localhost:8000                      │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. Project-Specific Commands

### Quick Reference

```bash
# ===== GIT COMMANDS =====
# View Git status
git status

# View commit history
git log --oneline

# Push changes
git add .
git commit -m "Your message"
git push origin main

# ===== DOCKER COMMANDS =====
# Build Docker image
cd C:\Users\DELL\OneDrive\Desktop\Todo_app_using_django-main\Todo_app_using_django-main
docker build -t django-todo .

# Run container
docker run -d -p 8000:8000 --name django-todo django-todo

# View logs
docker logs django-todo -f

# Stop and remove
docker stop django-todo && docker rm django-todo

# ===== JENKINS COMMANDS =====
# Start Jenkins
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins:lts

# Get password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword

# Restart Jenkins
docker restart jenkins

# ===== TESTING =====
# Test if app is running
curl http://localhost:8000/loginn/

# Check container status
docker ps --filter name=django-todo

# Check port usage
netstat -ano | findstr :8000
```

### Troubleshooting

| Problem | Solution |
|---------|----------|
| Port 8000 already in use | `docker rm -f django-todo` or use different port |
| Jenkins password forgotten | `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword` |
| Docker build fails | Check Dockerfile path and requirements.txt |
| Jenkins can't access Docker | Mount Docker socket: `-v /var/run/docker.sock:/var/run/docker.sock` |
| CSS not loading | Check STATICFILES_DIRS in settings.py |
| Git push fails | Check remote: `git remote -v` |

### File Structure

```
Todo_app_using_django-main/
├── Jenkinsfile              # CI/CD pipeline definition
├── Dockerfile               # Docker image definition
├── requirements.txt         # Python dependencies
├── README.md               # Project documentation
├── DOCS.md                 # This file - DevOps guide
├── .gitignore              # Files to ignore in Git
├── .dockerignore           # Files to ignore in Docker build
└── todo/
    ├── manage.py           # Django management script
    └── todo/
        ├── settings.py     # Django settings
        ├── urls.py         # URL routing
        ├── views.py        # Application logic
        ├── templates/      # HTML templates
        └── static/         # CSS, JS, images
```

---

## Summary

| Tool | Purpose | Key Benefit |
|------|---------|-------------|
| **Git** | Version Control | Track changes, collaborate |
| **Docker** | Containerization | Run anywhere, consistent |
| **Jenkins** | CI/CD Automation | Deploy automatically |

### The DevOps Cycle

```
     ┌──────────────┐
     │    PLAN      │
     └──────┬───────┘
            │
     ┌──────▼───────┐
     │     CODE     │ ← Git
     └──────┬───────┘
            │
     ┌──────▼───────┐
     │    BUILD     │ ← Docker
     └──────┬───────┘
            │
     ┌──────▼───────┐
     │    TEST      │ ← Jenkins
     └──────┬───────┘
            │
     ┌──────▼───────┐
     │   DEPLOY     │ ← Jenkins + Docker
     └──────┬───────┘
            │
     ┌──────▼───────┐
     │   MONITOR    │
     └──────┬───────┘
            │
            └──────────► Back to CODE (Continuous!)
```

---

## Additional Resources

- **Git:** https://git-scm.com/docs
- **Docker:** https://docs.docker.com/
- **Jenkins:** https://www.jenkins.io/doc/
- **Django:** https://docs.djangoproject.com/

---

*Created for Django Todo DevOps Project*  
*Author: DevOps Team*
