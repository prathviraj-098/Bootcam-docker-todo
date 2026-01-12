# My Todo App — Docker Compose Setup ✅

This repository contains a simple Node.js TODO app and instructions to run it with Docker Compose along with a Postgres service using a named volume for persistence.

---

## What I did (summary)
- Reviewed the repo and confirmed it runs a Node.js server at port 3000 (`src/index.js`)
- Found an existing `Dockerfile` that builds a Node image for the app
- Added `docker-compose.yml` which:
  - Builds the `frontend` image from the existing `Dockerfile` and maps host port `8000` -> container `3000`
  - Adds a `db` service using `postgres:15` and a **named volume** `postgres_data` for persistence
- Added this `README.md` with the commands and verification steps

---

## Files added 🔧
- `docker-compose.yml` — compose definition for `frontend` and `db` (Postgres) with `postgres_data` named volume

---

## How to build & run (commands)
1. Build the frontend image:

```bash
docker-compose build
```

2. Start both services in detached mode:

```bash
docker-compose up -d
```

3. Confirm containers are running:

```bash
docker ps
```

4. Check logs:

```bash
docker-compose logs
# follow frontend logs
docker-compose logs -f frontend
```

5. Enter the frontend container shell:

```bash
docker-compose exec frontend bash
# inside container, check files, e.g. `ls -la`, `cat /user/src/app/package.json`
```

6. Inspect a container (example):

```bash
# find container name from `docker ps`, e.g. my-todo-app_frontend_1
docker inspect <container_name> | jq '.[0].NetworkSettings'  # requires jq
# To find mounted volumes
docker inspect <container_name> | jq '.[0].Mounts'
```

7. Cleanup

```bash
docker-compose down
# Remove dangling images
docker image prune -f
# Clean other unused resources (without volumes):
docker system prune -f
```

---

## Notes & Explanations 💡
- The existing `src/persistence/index.js` picks the DB implementation by checking `process.env.POSTGRES_HOST`.
  - The `docker-compose.yml` sets `POSTGRES_HOST=db` so the frontend will use Postgres when run under Compose.
- `src/persistence/postgres.js` uses `wait-port` to wait for the DB to be available before connecting — this helps prevent race conditions on startup.
- I mapped host port `8000` to container `3000` (the app listens on `3000`) to avoid clashing with other services on port 80.
- A named volume `postgres_data` is used for `/var/lib/postgresql/data` so DB data persists across container restarts.

---

## Challenges you may face and fixes
- "App cannot connect to Postgres" — make sure your `db` service is healthy and that the `frontend` logs show attempts to connect; `depends_on` reduces race conditions but sometimes you need to retry or use compose `healthcheck`.
- If you changed DB creds, ensure both services use the same `POSTGRES_*` values.
- If port 8000 is taken, change the mapping in `docker-compose.yml` (
`ports: - "8001:3000"` for example).

---

## Verification checklist ✅
- [ ] `docker-compose build` completes without errors
- [ ] `docker-compose up -d` starts `frontend` and `db`
- [ ] `curl http://localhost:8000` returns the app HTML or `curl http://localhost:8000/items` returns JSON
- [ ] `docker ps` shows both containers
- [ ] `docker-compose logs -f frontend` shows server startup and DB connection messages

---

## Screenshots
Please take screenshots of your running app and `docker ps` output and add them below (or in a `screenshots/` folder). Example filenames:
- `screenshots/app_running.png`
- `screenshots/docker_ps.png`

---

If you'd like, I can also:
- Add a `.dockerignore`
- Add a small `healthcheck` for the Postgres service in `docker-compose.yml`
- Improve the `Dockerfile` to use a multi-stage build for a smaller production image

Let me know which (if any) of those you'd like me to do next.
