# =========================
# STAGE 1 — Dependencies
# =========================
FROM node:18-alpine AS dependencies
WORKDIR /app

# copy only dependency files first (for cache)
COPY package.json package-lock.json ./

# install production dependencies
RUN npm ci --only=production

# =========================
# STAGE 2 — Build
# =========================
FROM node:18-alpine AS build

# crete non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
WORKDIR /app

# Copy node modules from dependencies stage
COPY --from=dependencies /app/node_modules ./node_modules

COPY . .

# Chnage ownership of the app directory
RUN chown -R appuser:appgroup /app

USER appuser

# Expose app port
EXPOSE 3000

# Enviroment
ENV NODE_ENV=production \
    PORT=3000

# Healthcheck for Kubernenetes
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "src/index.js"]