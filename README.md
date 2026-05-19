# MERN Stack Dockerized App

A full-stack web application built with **MongoDB, Express, React, and Node.js**, containerized with Docker and deployable to AWS.

---

## Tech Stack

| Layer     | Technology        |
|-----------|-------------------|
| Frontend  | React (port 3000) |
| Backend   | Node.js + Express (port 5000) |
| Database  | MongoDB 6.0 (port 27017) |
| Container | Docker + Docker Compose |
| CI/CD     | GitHub Actions → AWS |

---

## Project Structure

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml       # CI/CD pipeline
├── client/                  # React frontend
│   └── Dockerfile
├── server/                  # Express backend
│   └── Dockerfile
└── docker-compose.yml
```

---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) & Docker Compose
- Node.js 18+ (for local dev without Docker)
- AWS account (for deployment)

---

## Getting Started

### Run with Docker (recommended)

```bash
# Clone the repo
git clone <your-repo-url>
cd <repo-name>

# Build and start all services
docker compose up --build

# Run in detached mode
docker compose up --build -d
```

Services will be available at:
- Frontend: http://localhost:3000
- Backend API: http://localhost:5000
- MongoDB: mongodb://localhost:27017

### Stop the app

```bash
docker compose down

# Stop and remove volumes (wipes MongoDB data)
docker compose down -v
```

---

## Services

### Frontend (`/client`)
React app served on port `3000`. Communicates with the backend via the internal `mern-network`.

### Backend (`/server`)
Express REST API on port `5000`. Connects to MongoDB using the service name `mongodb` as the host.

### MongoDB
Uses the official `mongo:6.0` image. Data is persisted in the `mongo-data` Docker volume.

---

## Environment Variables

Create a `.env` file in `/server` for backend config:

```env
PORT=5000
MONGO_URI=mongodb://mongodb:27017/yourdbname
```

> **Note:** Never commit `.env` files. Add them to `.gitignore`.

---

## CI/CD Pipeline

This project uses GitHub Actions to automatically build and deploy to an AWS EC2 instance on every push to `main`.

### Pipeline overview

1. Code pushed to `main`
2. GitHub Actions builds Docker images
3. Images pushed to Docker Hub / ECR
4. SSH into EC2, pull latest images, restart containers

See `.github/workflows/deploy.yml` for the full pipeline configuration.

### Required GitHub Secrets

| Secret | Description |
|--------|-------------|
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub password or access token |
| `EC2_HOST` | Public IP or DNS of your AWS EC2 instance |
| `EC2_USER` | SSH user (e.g. `ubuntu`) |
| `EC2_SSH_KEY` | Private SSH key for EC2 access |

---

## Deployment (AWS EC2)

### One-time server setup

```bash
# Install Docker on EC2
sudo apt update && sudo apt install -y docker.io docker-compose-v2
sudo usermod -aG docker ubuntu
```

### Manual deploy

```bash
ssh ubuntu@<your-ec2-ip>
cd <repo-name>
git pull
docker compose up --build -d
```

---

## Useful Commands

```bash
# View running containers
docker ps

# View logs for a service
docker compose logs backend
docker compose logs frontend

# Rebuild a single service
docker compose up --build backend

# Open MongoDB shell
docker exec -it mongodb mongosh
```

---

