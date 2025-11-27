# Multi-stage build for the CareerPlatformDatabase container
# Stage 1: Build db_visualizer dependencies (Node)
FROM node:18-slim AS dbviz-builder
WORKDIR /app/db_visualizer
COPY db_visualizer/package.json ./
RUN npm ci --only=production
# Copy server code (no .env files assumed or required at build time)
COPY db_visualizer/server.js ./

# Stage 2: Runtime with PostgreSQL client tools and Node
FROM debian:stable-slim

# Install minimal tools: PostgreSQL client (psql), mysql/mongo clients not needed for DB, but keeping for scripts resiliency is optional
# We install postgresql client as startup.sh relies on pg_isready/psql binaries under /usr/lib/postgresql/<version>/bin
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      ca-certificates \
      curl \
      gnupg \
      sudo \
      procps \
      postgresql-client \
      postgresql \
      sqlite3 \
    && rm -rf /var/lib/apt/lists/*

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

# Security harden: create non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

# Expose default db_visualizer port (can be overridden at runtime)
EXPOSE 3000

# Default command: start database init script
# Note: No references to .env files at build time. Runtime environment variables should be passed through Docker/Compose.
CMD ["/bin/bash", "-lc", "/app/startup.sh && node /app/db_visualizer/server.js --host 0.0.0.0"]
