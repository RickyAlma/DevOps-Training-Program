# 🐳 Multi-Service Docker Environment

A fully containerized application stack built with Python Flask API,
React frontend, and PostgreSQL database. Includes Redis caching,
Prometheus metrics, and Grafana dashboards — with multi-stage builds
for development, staging, and production environments.

---

## Quick Start


### 2. Configure Environment Variables

```bash
cp .env.example .env
nano .env   # Fill in your values
```

### 3. Build and Start

```bash
docker compose up --build
```
## 🌍 Environment Variants

This project uses Docker Compose override files to manage
three environments from the same codebase.

### Development (default)

Full hot reload for both API and frontend. Debug mode on.
Database credentials are hardcoded for convenience.

```bash
docker compose up --build
```

### Staging

Production-like environment. No bind mounts. Credentials
loaded from `.env`. Nginx serves the frontend.

```bash
docker compose -f docker-compose.yml \
               -f docker-compose.staging.yml up --build
```

### Production

Hardened. Non-root users. No exposed DB or Redis ports.
All credentials from `.env`. SSL-ready on ports 80 and 443.

```bash
docker compose -f docker-compose.yml \
               -f docker-compose.prod.yml up --build -d


```bash
# Check health status of all containers
docker compose ps

# Inspect a specific container's health
docker inspect --format='{{.Name}} → {{.State.Health.Status}}' appdb_dev


```bash
# List all project volumes
docker volume ls | grep docker-stack

# Inspect a volume
docker volume inspect docker-stack_postgres_dev_data
```

##  Networking

All services communicate over a private `app-network` bridge.
Servic

```bash
# Inspect the network
docker network inspect docker-stack_app-network

# Test service connectivity
docker exec flask_api_dev ping db
docker exec flask_api_dev ping redis
```

---

##  Monitoring

### Prometheus
Prometheus scrapes metrics from all services every 15 seconds.
Config is at `monitoring/prometheus.yml`.

Access at: http://localhost:9090

### Grafana
Grafana visualizes Prometheus metrics via dashboards.
Default login: `admin` / `admin`

Access at: http://localhost:3001

Recommended dashboard IDs to import:
- Flask API: `11074`
- PostgreSQL: `9628`
- Redis: `763`

---

## 📋 Logging

All services use the `json-file` log driver with rotation:

- Max file size: `10MB`
- Max files kept: `3`

```bash
# View logs for a service
docker compose logs -f api
docker compose logs -f db
docker compose logs -f frontend

