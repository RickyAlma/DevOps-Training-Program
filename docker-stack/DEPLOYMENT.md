# Deployment Guide

Step-by-step instructions for deploying the docker-stack
application across development, staging, and production.

---

## Prerequisites

Install these tools before proceeding:

```bash
# Check Docker version (must be 24.x+)
docker --version

# Check Docker Compose version (must be 2.x+)
docker compose version

# Check Git version
git --version
```

---

## 1. Clone and Configure

```bash

# Create environment file
cp .env.example .env
nano .env
```

---

## 2. Development Deployment

```bash
# Build and start all services
docker compose up --build

# Or run in background
docker compose up --build -d
```

Verify all services are running:

```bash
docker compose ps
```

Test services:

```bash
curl http://localhost:5000/health
curl http://localhost:5000/
```

---

## 3. Staging Deployment

```bash
# Bring down any running stack first
docker compose down -v

# Build and start staging
docker compose -f docker-compose.yml \
               -f docker-compose.staging.yml up --build
```

Verify:

```bash
docker compose -f docker-compose.yml \
               -f docker-compose.staging.yml ps
```

---

## 4. Production Deployment

```bash
# Bring down staging stack
docker compose -f docker-compose.yml \
               -f docker-compose.staging.yml down

# Build and start production in background
docker compose -f docker-compose.yml \
               -f docker-compose.prod.yml up --build -d
```

Verify:

```bash
docker compose -f docker-compose.yml \
               -f docker-compose.prod.yml ps
```

---

## 5. Production Checklist

Run through this before going live:

-  All `changeme_` values replaced in `.env`
-  `.env` is NOT committed to git
-  `docker compose config` shows no errors
-  All containers show `healthy` status
-  `curl http://localhost:5000/health` returns 200
-  Grafana accessible at port 3001
-  Prometheus accessible at port 9090
-  Database data persisted in named volume
-  Log rotation configured on all services

---

## 6. Monitoring Setup

### Prometheus
1. Open http://localhost:9090
2. Go to Status → Targets
3. Verify all scrape targets show `UP`

### Grafana
1. Open http://localhost:3001
2. Login: `admin` / your `GRAFANA_PASSWORD`
3. Go to Connections → Add data source
4. Select Prometheus
5. Set URL to `http://prometheus:9090`
6. Click Save & Test
7. Import dashboards:
   - Flask API metrics: ID `11074`
   - PostgreSQL metrics: ID `9628`
   - Redis metrics: ID `763`

---

## 7. Updating the Application

```bash
# Pull latest code
git pull origin main

# Rebuild and restart
docker compose up --build -d

# Verify update
docker compose ps
docker compose logs --tail=20 api
```

---

## 8. Rollback

```bash
# Stop current stack
docker compose down

# Checkout previous version
git checkout <previous-commit-hash>

# Rebuild with previous version
docker compose up --build -d
```

---

## 9. Backup Database

```bash
# Development backup
docker exec appdb_dev pg_dump -U devuser appdb_dev > backup_dev.sql

# Staging backup
docker exec appdb_staging pg_dump -U $DB_USER appdb_staging > backup_staging.sql

# Restore from backup
docker exec -i appdb_dev psql -U devuser appdb_dev < backup_dev.sql
```

---

## 10. Cleanup

```bash
# Stop and remove containers
docker compose down

# Remove containers and volumes (deletes all data)
docker compose down -v

# Remove all unused images
docker image prune -a

# Full cleanup
docker system prune -a --volumes
```
