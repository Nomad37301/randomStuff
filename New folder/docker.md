# Mastering Docker in a Day

This documentation is prepared for the **Docker Workshop** — a hands-on guide to containerization from environment setup to deploying multi-container applications.

## What is Docker?
Docker is an open-source platform that allows developers to package applications and their dependencies into lightweight, portable units called **containers**. Unlike traditional virtual machines, containers share the host OS kernel, making them faster and more efficient.

## Why Learn Docker?
- **Industry Standard:** Used by top companies worldwide to build, ship, and run applications consistently.
- **DevOps Essential:** Core tool in modern CI/CD pipelines and cloud deployments.
- **Isolation & Portability:** "It works on my machine" is no longer a problem — if it runs in a container, it runs everywhere.

---

## VM vs Container (Quick Comparison)

Before diving in, it helps to understand *why* Docker exists.

| | Virtual Machine (VM) | Container |
|---|---|---|
| OS | Full guest OS per VM | Shares host OS kernel |
| Size | GBs | MBs |
| Startup | Minutes | Seconds |
| Performance | Heavier | Lightweight |
| Isolation | Full | Process-level |

> **Bottom line:** Containers are not a replacement for VMs — they solve a different problem. Containers are ideal for running applications consistently across environments.

---

## Environment Setup

### 1. Install Docker Desktop (Windows / Mac)
Best for: Local development on personal machines.
- Download from [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
- Run the installer and follow the setup wizard.
- After installation, open a terminal and verify with:
```bash
docker --version
```

### 2. Install Docker on Linux (Ubuntu)
Best for: Servers or users running Ubuntu/WSL.
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```
Add your user to the docker group (so you don't need `sudo` every time):
```bash
sudo usermod -aG docker $USER
```
Then log out and back in for changes to take effect.

### 3. Verify Installation
```bash
docker run hello-world
```
If you see a welcome message from Docker — you're good to go! ✅

---

## Core Concepts

### Image
A **read-only template** used to create containers. Think of it like a blueprint or a snapshot of an application and its environment.

### Container
A **running instance** of an image. You can run multiple containers from the same image — each is isolated from the others.

### Docker Hub
A public registry where you can pull pre-built images (like pulling packages from npm or pip).
- Website: [https://hub.docker.com](https://hub.docker.com)

### Dockerfile
A text file with instructions for building a custom Docker image.

### Docker Compose
A tool for defining and running **multi-container** applications using a single `docker-compose.yml` file.

---

## Basic Docker Commands

- `docker pull <image>` — Download an image from Docker Hub.
- `docker images` — List all locally available images.
- `docker run <image>` — Create and start a container from an image.
- `docker run -d <image>` — Run a container in the background (detached mode).
- `docker run -p 8080:80 <image>` — Map host port `8080` to container port `80`.
- `docker ps` — List all **running** containers.
- `docker ps -a` — List **all** containers (including stopped ones).
- `docker exec -it <container_id> bash` — Open a shell inside a running container.
- `docker stop <container_id>` — Gracefully stop a running container.
- `docker rm <container_id>` — Remove a stopped container.
- `docker rmi <image>` — Remove a locally stored image.

---

## Dockerfile

A `Dockerfile` is how you create your own custom image.

### Basic Structure
```dockerfile
# Base image
FROM node:18-alpine

# Set working directory inside the container
WORKDIR /app

# Copy files from your machine to the container
COPY . .

# Install dependencies
RUN npm install

# Expose the port the app runs on
EXPOSE 3000

# Command to run when the container starts
CMD ["node", "index.js"]
```

### Build & Run Your Image
```bash
# Build image from Dockerfile in current directory
docker build -t my-app .

# Run the container and map port
docker run -p 3000:3000 my-app
```

Open your browser and go to `http://localhost:3000` to see your app! 🎉

---

## Volumes & Port Mapping

### Port Mapping
Containers are isolated — to access a service running inside a container, you need to map its port to your host machine.
```bash
# Format: docker run -p <host_port>:<container_port> <image>
docker run -p 8080:80 nginx
```
Then open `http://localhost:8080` in your browser.

### Volumes
By default, data inside a container is **lost** when the container is removed. Volumes solve this by persisting data on your host machine.
```bash
# Mount a local folder to a path inside the container
docker run -v /your/local/path:/app/data my-app
```
Or use a named volume:
```bash
docker volume create mydata
docker run -v mydata:/app/data my-app
```

---

## Docker Compose

Docker Compose lets you manage **multiple containers** at once using a single configuration file — perfect for real-world apps (e.g., a web server + database).

### Example: `docker-compose.yml`
```yaml
version: "3"
services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### Compose Commands
```bash
# Start all services defined in docker-compose.yml
docker compose up

# Start in background (detached)
docker compose up -d

# Stop all running services
docker compose down

# View logs from all services
docker compose logs
```

---

## What's Next?
Now that you understand the basics of Docker, here are some recommended next steps to continue your journey:
- **Docker Networking** — How containers talk to each other.
- **Docker Swarm / Kubernetes** — Orchestrating containers at scale.
- **CI/CD with Docker** — Automate image builds and deployments with GitHub Actions or GitLab CI.
- **Docker Security** — Best practices for hardening containers in production.