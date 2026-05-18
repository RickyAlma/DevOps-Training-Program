# ── Stage 1: Base ──────────────────────────────────────────
FROM postgres:15-alpine AS base

RUN apk add --no-cache curl
COPY init/ /docker-entrypoint-initdb.d/

# ── Stage 2: Development ───────────────────────────────────
FROM base AS development

ENV POSTGRES_DB=appdb_dev \
    POSTGRES_USER=devuser \
    POSTGRES_PASSWORD=devpassword

EXPOSE 5432

# ── Stage 3: Staging ───────────────────────────────────────
FROM base AS staging

EXPOSE 5432
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=5 \
    CMD pg_isready -U $POSTGRES_USER -d $POSTGRES_DB || exit 1

# ── Stage 4: Production ────────────────────────────────────
FROM base AS production

EXPOSE 5432
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=5 \
    CMD pg_isready -U $POSTGRES_USER -d $POSTGRES_DB || exit 1
