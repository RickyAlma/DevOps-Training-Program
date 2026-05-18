# Multi-Service Docker Environment

A fully containerized application stack built with Flask API, React frontend,
and PostgreSQL database — with multi-stage builds for development, staging,
and production environments.



## Getting Started

### 1. Configure Environment Variables

```bash
cp .env.example .env
nano .env
```

### 2. Build Docker Images

```bash
# Flask API
docker build --target development -t flask-api:dev ./api

# React Frontend
docker build --target development -t react-frontend:dev ./frontend

# PostgreSQL
docker build --target development -t postgres-db:dev ./db
```

### 3. Start All Services

```bash
docker compose up --buid

### Run Development

```bash
docker compose up --build
```

### Run Staging

```bash
docker compose -f docker-compose.yml -f docker-compose.staging.yml up --build
```

### Run Production

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up --build -d
```


```bash
# Check container health status
docker inspect --format='{{json .State.Health}}' <container_name>
```

---

## Security Notes

- Never commit `.env` — only `.env.example` is tracked
- Staging and production use non-root users
- Database credentials are passed via environment variables, not hardcoded
- Sensitive data should never be set via `ENV` in Dockerfiles in production

---

## Useful Commands

```bash
# View all built images
docker images

# View running containers
docker compose ps

# View logs for a service
docker compose logs -f api

# Stop all services
docker compose down

# Stop and remove volumes
docker compose down -v

# Access PostgreSQL shell
docker exec -it appdb_dev psql -U devuser -d appdb_dev
```

