# mvp-career-platform-db-1-214773-214790

CareerPlatformDatabase container no longer requires .env files at build time. Environment variables are read at runtime via Docker/Compose.

Build:
- docker build -t career-platform-db ./CareerPlatformDatabase

Run (example):
- docker run --rm -p 3000:3000 \
  -e POSTGRES_DB=myapp \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=dbuser123 \
  -e POSTGRES_PORT=5000 \
  career-platform-db

Notes:
- db_visualizer reads environment variables from process.env. Optional local files like postgres.env can be used during development (not required).
- See CareerPlatformDatabase/.env.example for available keys.