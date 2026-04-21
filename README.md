# HNG Stage 2: DevOps Deployment Guide

This repository contains the containerized full-stack application for the HNG Stage 2 DevOps task. It features a FastAPI backend, a Next.js frontend, and a Redis instance, all managed via Docker Compose and secured with an automated CI/CD pipeline.

---

## 🛠️ Prerequisites

Ensure your host machine has the following installed:

- Docker (v20.10 or higher)
- Docker Compose (V2)
- Git

---

## 🚀 Deployment from Scratch

Follow these steps to bring the entire stack up on a clean machine:

### 1. Clone the Repository

```bash
git clone https://github.com/Jaymi-01/hng14-stage2-devops.git
cd hng14-stage2-devops
```

### 2. Configure Environment Variables

The application requires a `.env` file to manage secrets and service discovery.

Copy the template provided in the repo:

```bash
cp .env.example .env
```

Open `.env` and set a secure password for `REDIS_PASSWORD`:

```bash
# Example:
REDIS_PASSWORD=YourSecurePassword123
nano .env
```

### 3. Build and Start the Stack

This command builds the production-ready multi-stage images and starts the containers in the background.

```bash
docker compose up -d --build
```

---

## ✅ Verifying a Successful Startup

A successful deployment is confirmed when all containers report a `(healthy)` status.

### Check Service Status

```bash
docker compose ps
```

### Expected Output Indicators

| Service  | Status        | Port Mapping  | Purpose           |
|----------|---------------|---------------|-------------------|
| api      | Up (healthy)  | 8000          | FastAPI Backend   |
| frontend | Up (healthy)  | 3000 -> 3000  | Next.js Web UI    |
| redis    | Up (healthy)  | Internal      | Data Storage      |
| worker   | Up            | Internal      | Task Processor    |

### Access the Stack

- **Web Frontend:** http://localhost:3000
- **API Documentation:** http://localhost:8000/docs

---

## 🏗️ DevOps Architecture & Security

- **Multi-Stage Builds:** Optimized Dockerfiles to ensure production images are lean and free of build-time dependencies.

- **Non-Root Execution:** Security-first approach where all services run as non-privileged users (`appuser`/`nodeuser`) to minimize attack surface.

- **Network Isolation:** All services communicate over an isolated internal bridge network (`hng_internal_network`), with only necessary ports exposed to the host.

- **Automated CI/CD Pipeline:** Every push to the `main` branch triggers a GitHub Actions workflow that executes:
  - **Linting:** Automated code quality checks using Flake8 (Python), ESLint (Javascript), and Hadolint (Docker).
  - **Security Scanning:** Trivy vulnerability scanning with SARIF report generation.
  - **Testing:** Comprehensive unit testing via Pytest and automated integration shell scripts.

- **Resource Management:** Docker Compose is configured with explicit CPU/Memory limits and `always` restart policies for reliability.