# 🐳 Docker Learning Project: Node.js + MongoDB

This is a comprehensive reference project for learning Docker with a real-world Node.js application and MongoDB database. It includes detailed comments throughout all configuration files to help you understand each concept.

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Docker Concepts Explained](#docker-concepts-explained)
- [Getting Started](#getting-started)
- [Development Commands](#development-commands)
- [Production Commands](#production-commands)
- [API Endpoints](#api-endpoints)
- [Common Docker Commands Reference](#common-docker-commands-reference)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

---

## 🎯 Project Overview

This project demonstrates:

- ✅ Building Docker images with multi-stage builds
- ✅ Using Docker Compose for multi-container applications
- ✅ Containerizing a TypeScript Node.js REST API
- ✅ Compiling TypeScript in Docker and copying only compiled code to production
- ✅ Connecting to MongoDB in containers
- ✅ Development vs Production configurations
- ✅ Volume management for data persistence
- ✅ Networking between containers
- ✅ Health checks and monitoring

---

## 📦 Prerequisites

Before starting, ensure you have installed:

1. **Docker Desktop** (includes Docker Engine and Docker Compose)

   - Windows/Mac: [Download Docker Desktop](https://www.docker.com/products/docker-desktop)
   - Linux: Install Docker Engine and Docker Compose separately

2. Verify installation:
   ```bash
   docker --version
   docker-compose --version
   ```

---

## 📁 Project Structure

```
dockerc/
│   └── server.ts             # TypeScript application code
├── dist/                     # Compiled JavaScript (generated)
├── package.json              # Node.js dependencies
├── tsconfig.json             # TypeScript configurationcode
├── package.json              # Node.js dependencies
├── Dockerfile.dev            # Development Dockerfile
├── Dockerfile.prod           # Production Dockerfile
├── docker-compose.yml        # Development environment setup
├── docker-compose.prod.yml   # Production environment setup
├── .dockerignore            # Files to exclude from Docker build
├── .env.example             # Example environment variables
└── README.md                # This file
```

---

## 🧠 Docker Concepts Explained

### What is Docker?

Docker is a platform that packages applications and their dependencies into **containers** - lightweight, portable units that run consistently across different environments.

### Key Concepts:

#### 1. **Docker Image**

- A read-only template containing application code, runtime, libraries, and dependencies
- Built from instructions in a `Dockerfile`
- Think of it as a "snapshot" or "template" of your application

#### 2. **Docker Container**

- A running instance of a Docker image
- Isolated environment that includes everything needed to run your app
- Multiple containers can be created from one image

#### 3. **Dockerfile**

- A text file with instructions to build a Docker image
- Contains commands like `FROM`, `COPY`, `RUN`, `CMD`
- Each instruction creates a new layer in the image

#### 4. **Docker Compose**

- A tool for defining multi-container applications
- Uses a YAML file (`docker-compose.yml`) to configure services
- Simplifies running complex applications with one command

#### 5. **Volumes**

- Persistent storage for containers
- Data survives even when containers are deleted
- Used for databases, uploaded files, etc.

#### 6. **Networks**

- Allow containers to communicate with each other
- Docker Compose automatically creates a network for your services
- Containers can reach each other using service names as hostnames

#### 7. **Multi-stage Builds**

- Use multiple `FROM` statements in one Dockerfile
- Build dependencies in one stage, copy only needed files to final stage
- Results in smaller, more secure production images

---

## 🚀 Getting Started

### Step 1: Clone and Setup

```bash
# Navigate to project directory
cd d:\Demo-Projects\Docker

# Copy the example environment file
copy .env.example .env

# (Optional) Edit .env file to customize settings
```

### Step 2: Install Dependencies Locally (Optional)

# Install dependencies

npm install

# Build TypeScript (optional - Docker will do this)

npm run buildional - Docker will install dependencies inside the container.

```bash
npm install
```

### Step 3: Choose Your Path

Continue with either [Development Commands](#development-commands) or [Production Commands](#production-commands).

---

## 💻 Development Commands

### Using Docker Compose (Recommended for Development)

#### Start the Application

```bash
# Start all services (app + MongoDB + Mongo Express)
docker-compose -f docker-compose.dev.yml up

# Or start in detached mode (runs in background)
docker-compose -f docker-compose.dev.yml up -d
```

**What this does:**

- Builds the Node.js app image (if not already built)
- Starts the Node.js container
- Starts MongoDB container
- Starts Mongo Express (web UI for MongoDB)
- Creates a network for containers to communicate
- Creates volumes for MongoDB data persistence
- Enables hot-reloading (code changes auto-restart the app)

#### View Logs

```bash
# View logs from all services
docker-compose -f docker-compose.dev.yml logs

# View logs from specific service
docker-compose -f docker-compose.dev.yml logs app
docker-compose -f docker-compose.dev.yml logs mongodb

# Follow logs in real-time
docker-compose -f docker-compose.dev.yml logs -f app
```

#### Stop the Application

```bash
# Stop all services (keeps containers)
docker-compose -f docker-compose.dev.yml stop

# Stop and remove containers (but keeps volumes)
docker-compose -f docker-compose.dev.yml down

# Stop, remove containers AND delete volumes (deletes database data)
docker-compose -f docker-compose.dev.yml down -v
```

#### Rebuild After Code Changes

```bash
# Rebuild and restart services
docker-compose -f docker-compose.dev.yml up --build

# Force rebuild without cache
docker-compose -f docker-compose.dev.yml build --no-cache
```

#### Access Running Containers

```bash
# Open shell in app container
docker-compose -f docker-compose.dev.yml exec app sh

# Open MongoDB shell
docker-compose -f docker-compose.dev.yml exec mongodb mongosh

# View running containers
docker-compose -f docker-compose.dev.yml ps
```

### Using Docker Directly (Without Compose)

#### Build the Image

```bash
# Build development image using Dockerfile.dev
docker build -f Dockerfile.dev -t nodejs-app:dev .
```

#### Run MongoDB Separately

```bash
# Create a network
docker network create app-network

# Run MongoDB
docker run -d \
  --name mongodb \
  --network app-network \
  -p 27017:27017 \
  -v mongodb-data:/data/db \
  mongo:7
```

#### Run the Application

```bash
# Run the app container
docker run -d \
  --name nodejs-app \
  --network app-network \
  -p 3000:3000 \
  -e MONGO_URI=mongodb://mongodb:27017/dockerapp \
  -v ${PWD}:/app \
  -v /app/node_modules \
  nodejs-app:dev
```

---

## 🏭 Production Commands

### Using Docker Compose for Production

#### Build Production Images

```bash
# Build production images
docker-compose -f docker-compose.prod.yml build

# Build without cache (fresh build)
docker-compose -f docker-compose.prod.yml build --no-cache
```

#### Start Production Environment

```bash
# Start production services in detached mode
docker-compose -f docker-compose.prod.yml up -d

# View logs
docker-compose -f docker-compose.prod.yml logs -f
```

#### Stop Production Environment

```bash
# Stop services
docker-compose -f docker-compose.prod.yml down

# Stop and remove volumes (caution: deletes data!)
docker-compose -f docker-compose.prod.yml down -v
```

### Using Docker Directly (Production)

#### Build Production Image

```bash
# Build production image using Dockerfile.prod
docker build -f Dockerfile.prod -t nodejs-app:prod .
```

#### Run Production Containers

```bash
# Create network
docker network create app-network-prod

# Run MongoDB with authentication
docker run -d \
  --name mongodb-prod \
  --network app-network-prod \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=securepwd123 \
  -v mongodb-data-prod:/data/db \
  mongo:7

# Run the application
docker run -d \
  --name nodejs-app-prod \
  --network app-network-prod \
  -p 3000:3000 \
  -e NODE_ENV=production \
  -e MONGO_URI=mongodb://admin:securepwd123@mongodb-prod:27017/dockerapp?authSource=admin \
  nodejs-app:prod
```

---

## 🔌 API Endpoints

Once the application is running, access it at: `http://localhost:3000`

### Available Endpoints:

| Method | Endpoint         | Description                   |
| ------ | ---------------- | ----------------------------- |
| GET    | `/`              | Welcome message with API info |
| GET    | `/health`        | Health check endpoint         |
| GET    | `/api/tasks`     | Get all tasks                 |
| POST   | `/api/tasks`     | Create a new task             |
| PUT    | `/api/tasks/:id` | Update a task                 |
| DELETE | `/api/tasks/:id` | Delete a task                 |

### Example API Calls:

```bash
# Check health
curl http://localhost:3000/health

# Get all tasks
curl http://localhost:3000/api/tasks

# Create a new task
curl -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn Docker"}'

# Update a task (replace <id> with actual task ID)
curl -X PUT http://localhost:3000/api/tasks/<id> \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'

# Delete a task (replace <id> with actual task ID)
curl -X DELETE http://localhost:3000/api/tasks/<id>
```

### Access Mongo Express (Development Only)

- URL: `http://localhost:8081`
- Username: `admin`
- Password: `admin123`

---

## 📚 Common Docker Commands Reference

### Image Management

```bash
# List all images
docker images

# Remove an image
docker rmi <image-name>

# Remove all unused images
docker image prune -a

# Build an image
docker build -t <name>:<tag> .

# Tag an image
docker tag <source-image> <target-image>
```

### Container Management

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start a container
docker start <container-name>

# Stop a container
docker stop <container-name>

# Restart a container
docker restart <container-name>

# Remove a container
docker rm <container-name>

# Remove all stopped containers
docker container prune

# View container logs
docker logs <container-name>
docker logs -f <container-name>  # Follow logs

# Execute command in running container
docker exec -it <container-name> sh
docker exec -it <container-name> bash

# View container details
docker inspect <container-name>

# View container resource usage
docker stats
```

### Volume Management

```bash
# List volumes
docker volume ls

# Create a volume
docker volume create <volume-name>

# Remove a volume
docker volume rm <volume-name>

# Remove all unused volumes
docker volume prune

# Inspect a volume
docker volume inspect <volume-name>
```

### Network Management

```bash
# List networks
docker network ls

# Create a network
docker network create <network-name>

# Remove a network
docker network rm <network-name>

# Inspect a network
docker network inspect <network-name>

# Connect container to network
docker network connect <network-name> <container-name>
```

### System Management

```bash
# View disk usage
docker system df

# Clean up unused resources
docker system prune

# Clean up everything (caution!)
docker system prune -a --volumes

# View Docker info
docker info

# View Docker version
docker version
```

---

## 🐛 Troubleshooting

### Problem: Port Already in Use

**Error:** `Bind for 0.0.0.0:3000 failed: port is already allocated`

**Solution:**

```bash
# Option 1: Find and stop the process using the port
# Windows:
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Linux/Mac:
lsof -i :3000
kill -9 <PID>

# Option 2: Change port in docker-compose.dev.yml
# Change "3000:3000" to "3001:3000"
```

### Problem: Cannot Connect to MongoDB

**Error:** `MongooseServerSelectionError: connect ECONNREFUSED`

**Solutions:**

1. Ensure MongoDB container is running:

   ```bash
   docker-compose -f docker-compose.dev.yml ps
   ```

2. Check MongoDB logs:

   ```bash
   docker-compose -f docker-compose.dev.yml logs mongodb
   ```

3. Verify network connectivity:

   ```bash
   docker-compose -f docker-compose.dev.yml exec app ping mongodb
   ```

4. Restart services:
   ```bash
   docker-compose -f docker-compose.dev.yml restart
   ```

### Problem: Changes Not Reflected

**Issue:** Code changes don't appear in the running app

**Solutions:**

1. For development, ensure volumes are mounted correctly
2. Check if nodemon is running in container logs
3. Rebuild the image:
   ```bash
   docker-compose -f docker-compose.dev.yml up --build
   ```

### Problem: Out of Disk Space

**Error:** `no space left on device`

**Solution:**

```bash
# Clean up unused Docker resources
docker system prune -a --volumes

# Remove specific volumes
docker volume rm <volume-name>
```

### Problem: Permission Denied

**Issue:** Files created by containers have wrong permissions

**Solution:**

```bash
# On Linux/Mac, run container with your user ID
docker run --user $(id -u):$(id -g) ...

# Or fix permissions after:
sudo chown -R $USER:$USER .
```

---

## ✨ Best Practices

### 1. **Security**

- ✅ Never commit `.env` files with secrets
- ✅ Run containers as non-root users
- ✅ Use secrets management in production
- ✅ Enable MongoDB authentication in production
- ✅ Keep images updated with security patches

### 2. **Image Optimization**

- ✅ Use multi-stage builds
- ✅ Use Alpine-based images when possible
- ✅ Leverage Docker layer caching
- ✅ Use `.dockerignore` to exclude unnecessary files
- ✅ Minimize the number of layers

### 3. **Development Workflow**

- ✅ Use Docker Compose for local development
- ✅ Mount source code as volumes for hot-reloading
- ✅ Use separate configs for dev and prod
- ✅ Add health checks to services
- ✅ Use named volumes for data persistence

### 4. **Production Deployment**

- ✅ Use specific image tags (not `latest`)
- ✅ Set resource limits
- ✅ Implement proper logging
- ✅ Use orchestration tools (Kubernetes, Docker Swarm)
- ✅ Set up monitoring and alerting
- ✅ Regular backups of volumes

### 5. **Performance**

- ✅ Order Dockerfile commands from least to most frequently changing
- ✅ Use `.dockerignore` to reduce build context
- ✅ Combine RUN commands to reduce layers
- ✅ Clean up in the same layer where you install

---

## 📖 Learning Resources

### Official Documentation

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### Useful Commands Cheat Sheets

- [Docker Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
- [Docker Compose Cheat Sheet](https://devhints.io/docker-compose)

---

## 🎓 Next Steps

After mastering this project, explore:

1. **Container Orchestration**

   - Learn Kubernetes basics
   - Try Docker Swarm

2. **CI/CD Integration**

   - Automate builds with GitHub Actions
   - Deploy to cloud platforms

3. **Advanced Topics**

   - Docker Secrets
   - Multi-host networking
   - Container security scanning

4. **Monitoring & Logging**
   - Set up Prometheus + Grafana
   - Centralized logging with ELK stack

---

## 📝 License

This project is open source and available for learning purposes.

---

## 🤝 Contributing

Feel free to fork this project and customize it for your learning needs!

---

**Happy Learning! 🐳**
