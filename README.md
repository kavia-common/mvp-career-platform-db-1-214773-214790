# mvp-career-platform-db-1-214773-214790

Hardened CareerPlatformDatabase container:
- No .env files are required or used at build-time.
- Runtime environment variables are supplied via Docker/Compose/Kubernetes only.
- Local .env files are excluded from the Docker build context by .dockerignore.
- A .env.example is provided to document expected variables.

Build:
- docker build -t career-platform-db ./CareerPlatformDatabase

Run (example):
- docker run --rm -p 3000:3000 \
  -e POSTGRES_DB=myapp \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=dbuser123 \
  -e POSTGRES_PORT=5000 \
  career-platform-db

docker-compose (example):
services:
  db:
    image: career-platform-db:latest
    ports:
      - "3000:3000"
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: dbuser123
      POSTGRES_PORT: 5000

Notes:
- db_visualizer reads environment variables from process.env; it does not require .env files. If you choose to keep local files for development, provide variables via your shell or compose (avoid wildcard globs).
- See CareerPlatformDatabase/.env.example for available keys.
- Ensure that no .env is copied or read during image build. All configuration is runtime-only.