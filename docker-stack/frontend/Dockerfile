# ── Stage 1: Base ──────────────────────────────────────────
FROM node:20-alpine AS base

WORKDIR /app

COPY package*.json ./
RUN npm install

# ── Stage 2: Development ───────────────────────────────────
FROM base AS development

COPY . .
EXPOSE 3000
CMD ["npm", "start"]

# ── Stage 3: Build ─────────────────────────────────────────
FROM base AS build

ARG REACT_APP_API_URL
ENV REACT_APP_API_URL=$REACT_APP_API_URL

COPY . .
RUN npm run build

# ── Stage 4: Staging ───────────────────────────────────────
FROM nginx:1.25-alpine AS staging

COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.staging.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD curl -f http://localhost/health || exit 1
CMD ["nginx", "-g", "daemon off;"]

# ── Stage 5: Production ────────────────────────────────────
FROM nginx:1.25-alpine AS production

COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.prod.conf /etc/nginx/conf.d/default.conf

RUN addgroup -S webgroup && adduser -S webuser -G webgroup && \
    chown -R webuser:webgroup /usr/share/nginx/html

EXPOSE 80 443
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD curl -f http://localhost/health || exit 1
CMD ["nginx", "-g", "daemon off;"]
