# My Todo App — Docker Compose Assignment

**Objective:** Build and manage a simple multi-service To‑Do app using Docker and Docker Compose (frontend + Postgres DB).

---

## What I did
- Created/updated `Dockerfile` for the frontend and added `docker-compose.yml` defining `frontend` and `db` (Postgres) with a named volume `postgres_data` for persistence.
- Verified the frontend is accessible (mapped to host port `8000`) and that both containers run with `docker-compose up -d`.

---

## Preview

![App preview](Public/preview.png)

---

## Commands used
```bash
docker-compose build
docker-compose up -d
docker ps
curl http://localhost:8000
docker-compose logs -f frontend
docker-compose exec frontend bash
docker inspect <container_name>
docker-compose down
docker image prune -f
docker system prune -f
```

---

## Challenges & fixes
- Startup race between frontend and Postgres — addressed by using the `POSTGRES_HOST=db` env var and by waiting/retrying (e.g., `wait-port` or a healthcheck) before the app connects.

---

## What I learned
- How to write a `Dockerfile` for a frontend, define services in `docker-compose.yml`, use named volumes for Postgres persistence, and verify/inspect running containers.
