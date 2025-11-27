# Multi-stage build for the CareerPlatformDatabase container
# Stage 1: Build db_visualizer dependencies (Node) - no .env used at build time
FROM node:18-slim AS dbviz-builder
WORKDIR /app/db_visualizer
COPY db_visualizer/package.json ./
RUN npm ci --only=production
# Copy server code (no .env files assumed or required at build time)
COPY db_visualizer/server.js ./

# Stage 2: Runtime based on official Postgres image to avoid any .env build coupling
FROM postgres:16-alpine

# Install minimal tools (sqlite3, curl) and node runtime for the viewer
# We do NOT copy or source any .env at build time
RUN apk add --no-cache bash sudo curl procps sqlite

# Create app directory
WORKDIR /app

# Copy runtime scripts and resources
COPY backup_db.sh ./backup_db.sh
COPY restore_db.sh ./restore_db.sh
COPY startup.sh ./startup.sh

# Copy db_visualizer runtime from builder stage
COPY --from=dbviz-builder /app/db_visualizer /app/db_visualizer

# Make scripts executable
RUN chmod +x /app/backup_db.sh /app/restore_db.sh /app/startup.sh

# Security: create non-root user and chown app files (postgres user managed by base image)
RUN adduser -D appuser && chown -R appuser:appuser /app
USER appuser

# Expose default db_visualizer port (can be overridden at runtime)
EXPOSE 3000

# Default command: start database init script (uses runtime env only) then start viewer
# No ARG/RUN steps read .env; rely on POSTGRES_* runtime environment variables.
CMD ["/bin/bash", "-lc", "/app/startup.sh && node /app/db_visualizer/server.js --host 0.0.0.0"]
