# DevOps HW – Docker

This repository contains a fully containerized, production‑like web application stack developed as part of the DevOp homework. The goal of the project was to secure, and deploy a real‑world multi‑service application using Docker, Docker Compose, GitHub Actions (CI/CD), and TLS.

---

## Live Deployment

The application stack is deployed on the university DevOps VM and is publicly accessible over HTTPS:

**[https://devops-sk-07.lrk.si](https://devops-sk-07.lrk.si)**

TLS is provided automatically via **Let’s Encrypt**, managed by **Caddy**.

---

## Architecture Overview

The application consists of **five independent services**, orchestrated using Docker Compose:

| Service      | Description                             |
| ------------ | --------------------------------------- |
| **frontend** | Next.js / Node.js web frontend          |
| **backend**  | PHP backend API                         |
| **mysql**    | MySQL 8 database (persistent storage)   |
| **redis**    | Redis cache service                     |
| **caddy**    | Reverse proxy + HTTPS (TLS termination) |

All services communicate over a dedicated Docker bridge network `net`.

The architecture differs from hw1, because a reverse proxy (caddy) was added to allow for easier use of TLS and increased security.

All services are spun up from docker compose. Backend and frontend have their own dockerfiles due to more complex initialization. Mysql mounts a persistent data volume and an init script. Redis uses the default image without customization and caddy has some mounted volumes.

Frontend no longer uses a self-signed certificate for TLS.

---

## Docker & Docker Compose

The entire stack is defined declaratively in `docker-compose.yml` and can be started with:

```bash
docker compose up -d
```

### Volumes

Persistent data is stored using Docker volumes:

* `mysql-data` – MySQL database data
* `caddy_data` – TLS certificates (Let’s Encrypt)
* `caddy_config` – Caddy configuration

This ensures data persistence across container restarts.

---

## Custom Images & Multi‑Stage Builds

At least one service (**frontend**) is built using a **multi‑stage Dockerfile**:

1. **Build stage** – installs dependencies and builds the application
2. **Runtime stage** – contains only the minimal runtime environment

This significantly reduces the final image size and attack surface.

### BuildX

Docker **BuildX** is used in CI/CD to optimize the build process and support advanced build features.

---

## CI/CD – GitHub Actions

A complete CI/CD pipeline is implemented using **GitHub Actions**.

### What it does

* Triggers on `push` and `pull_request`
* Builds Docker images using BuildX
* Automatically tags images
* Pushes images to **GitHub Container Registry (GHCR)**
 Published images are visible under the repository’s **Packages** section.

> Automatic deployment is intentionally not enabled, as per assignment instructions.

---

## TLS / HTTPS Configuration

TLS is configured using **Caddy**:

* Automatic HTTPS
* Let’s Encrypt certificates
* Automatic renewal
* Uses **TLS‑ALPN‑01** challenge (required due to restricted port 80)


---
## Security
* No exposed ports other than 80, 443
* Only accessible via https
* No default credentials
* Only access to frontend via reverse proxy
---
## Deployment

The stack is deployed on the university VM:

* **Host:** `devops-sk-07.lrk.si`
* **Access:** public internet
* **Security:** HTTPS only

All services run inside Docker containers; no application services are exposed directly to the host except via Caddy.

---

## Repository Structure (Simplified)

```
.
├── .github/workflows/
│   └── publish.yml
├── backend/
│   └── Dockerfile
├── frontend/
│   └── Dockerfile
├── init/
│   ├── 01-init.sql
│   └── taprav-fri.sql
├── Caddyfile
├── README.md
└── docker-compose.yml
```

---

## Notes & Limitations

* Port **80** is blocked on the VM → TLS‑ALPN‑01 challenge is used instead of HTTP‑01
* CI/CD builds images but does not auto‑deploy (by design)
* Secrets are managed via environment variables and Docker volumes

---
