# Microservices-Based Node.js Application with Docker & Docker Compose

This project demonstrates a **microservices-based** application using **Node.js, MongoDB, and Docker Compose**. The application consists of the following components:

- **Web Service**: Serves HTTP requests.
- **Worker Service**: Background process that interacts with the database.
- **MongoDB Database**: Stores application data.
- **Mongo Express**: Web-based UI to manage MongoDB.

The setup ensures **containerized services**, proper **networking**, and **data persistence** using Docker volumes.

---

## 📌 **Project Structure**
```
📁 DevOps - Assignment/
 ├── 📁 web-service/        # Web Service (Express.js)
 │    ├── Dockerfile
 │    ├── index.js
 │    ├── package.json
 ├── 📁 worker-service/     # Worker Service (Node.js)
 │    ├── Dockerfile
 │    ├── worker.js
 │    ├── package.json
 ├── docker-compose.yml    # Docker Compose Configuration
 ├── README.md             # Project Documentation
```

---

## 🚀 **How to Run the Application**

### **Step 1: Clone the Repository**
```sh
git clone <repository_url>
cd DevOps-Assignment
```

### **Step 2: Set Up Environment Variables**
In `docker-compose.yml`, we've already set up environment variables for MongoDB authentication.

### **Step 3: Build & Run the Containers**
Run the following command to **build and start** all services:
```sh
docker-compose up --build
```

This will start:
- **Web Service** on `http://localhost:8080`
- **Mongo Express** on `http://localhost:8082`

---

## 🛠️ **Services Overview & Configurations**

### **1️⃣ Web Service (Express.js)**
- Runs in a **container** with the name: `RollNo_web`
- Exposes port **8081** (mapped to **8080** on localhost)
- Connects to MongoDB

---

### **2️⃣ Worker Service (Node.js)**
- Runs in a **container** with the name: `RollNo_worker`
- Performs background tasks (e.g., fetching data from MongoDB)

---

### **3️⃣ MongoDB (Database Service)**
- Runs in a **container** with the name: `mongo`
- Uses **Docker volume** for data persistence

---

### **4️⃣ Mongo Express (Database UI)**
- Runs in a **container** with the name: `mongo-express`
- Accessible at: `http://localhost:8082`

---

### ✅ **Check Running Containers**
```sh
docker ps
```

# Jenkins CI/CD Pipeline Setup

This repository contains a CI/CD pipeline implementation using Jenkins for automated build, test, and deployment processes.

## 🔧 Jenkins Setup

### Step 1: Installation
- Installed Jenkins server running on port 9090
- Required plugins:
  - Pipeline
  - Docker Pipeline
  - SonarQube Scanner
  - Blue Ocean
  - Git Plugin

### Step 2: Credentials Configuration
- GitHub Access Token (for repository access)
- Docker Hub Credentials:
  - Username: Your Docker Hub username
  - Password: Access Token (generated from Docker Hub settings)

### Step 3: Pipeline Stages
Our Jenkinsfile implements the following stages:

1. **Clone Repository**: Fetches the latest code from GitHub
2. **Build Docker Images**: Builds web-service and worker-service images
3. **Tag & Push Images**: Tags images with 'latest' and pushes to Docker Hub
4. **Run Containers**: Deploys containers (skipped if previous stage fails)
5. **Cleanup**: Removes temporary files (skipped if pipeline fails)

## 🐳 Docker Hub Integration

### Authentication Setup
We use Docker Hub Access Tokens instead of passwords:

1. Go to Docker Hub → Account Settings → Security
2. Generate a new Access Token
3. In Jenkins:
   - Navigate to Manage Jenkins → Manage Credentials
   - Add new credential with:
     - Username: Your Docker Hub username
     - Password: The generated Access Token

### Manual Image Push Commands
If needed, images can be pushed manually:

```bash
docker login
docker tag worker-service <dockerhub-username>/worker-service:latest
docker push <dockerhub-username>/worker-service:latest

📌 Troubleshooting
Plugin Dependency Issues
Error:
Copyjava.io.IOException: Failed to load: Docker Pipeline
Update required: Pipeline: Declarative to be updated to 2.2247 or higher
Fix:
Go to Manage Jenkins → Manage Plugins → Update all pending plugins → Restart Jenkins
Docker Hub Push Failure

Error:
CopyERROR: Could not find credentials matching docker-hub
Fix:
Verify Docker Hub credentials are correctly stored in Jenkins
Use Access Token instead of password
Ensure correct credential ID is referenced in the Jenkinsfile

🎯 Summary

✅ Automated build, test, and deployment pipeline
✅ Remote image storage via Docker Hub integration
✅ Simplified local development with Docker Compose