# Changelog

All notable changes are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.0.0] - 2026-05-21

### Added

#### Dockerfiles
- Multi-stage Dockerfile for Python Flask API
  - Stages: base, development, staging, production
  - Non-root user in staging and production
  - Gunicorn as production WSGI server
  - Health check on production stage

- Multi-stage Dockerfile for React Frontend
  - Stages: base, development, build, staging, production
  - Nginx serves static files in staging and production
  - Separate Nginx configs per environment
  - Security headers in production Nginx config

- Multi-stage Dockerfile for PostgreSQL
  - Stages: base, development, staging, production
  - SQL init scripts on first startup
  - Health checks on all stages

#### Docker Compose
- Base `docker-compose.yml` for development environment
- `docker-compose.staging.yml` override for staging
- `docker-compose.prod.yml` override for production
- Named volumes for data persistence across restarts
- Bridge network for inter-service communication
- Health checks on all six services
- JSON file logging with rotation on all services

#### Services
- Flask API with `/health` and `/` endpoints
- React frontend with hot reload in development
- PostgreSQL with init SQL scripts
- Redis cache service
- Prometheus metrics collection
- Grafana monitoring dashboards

#### Configuration
- `.env.example` environment variable template
- `monitoring/prometheus.yml` scrape configuration
- `frontend/nginx.staging.conf` for staging
- `frontend/nginx.prod.conf` for production with caching

### Fixed
- Replaced `npm ci` with `npm install` — no package-lock.json
- Removed `apk add curl` from frontend — DNS issue in container
- Corrected wrong content pasted into `requirements.txt`
- Resolved `role devuser does not exist` by clearing old volumes
- Created missing `nginx.staging.conf` for frontend staging build

### Security
- Non-root users in staging and production images
- DB and Redis ports not exposed in staging and production
- Secrets managed via `.env` file, never hardcoded in production
- `.env` excluded from git via `.gitignore`
