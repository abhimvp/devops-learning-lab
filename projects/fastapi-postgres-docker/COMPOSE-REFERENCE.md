# Compose File Reference

This file explains what `compose.yaml` is doing and what each section contains.

## Current compose.yaml

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    networks:
      - app-network
    depends_on:
      - db
    develop:
      watch:
        - action: sync
          path: .
          target: /app
  db:
    image: postgres:18.3-alpine3.23
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - pg-data:/var/lib/postgresql/data
    networks:
      - app-network
volumes:
  pg-data:
networks:
  app-network:
```

## Top-level sections

- `services`
  - Defines the containers that run together as one application stack.
- `volumes`
  - Defines named persistent storage used by services.
- `networks`
  - Defines virtual networks that allow services to communicate.

## Service: app

- `build.context: .`
  - Builds the image from the current project directory.
- `build.dockerfile: Dockerfile`
  - Uses the project Dockerfile for building the app image.
- `ports: "8000:8000"`
  - Maps host port `8000` to container port `8000`.
- `networks: app-network`
  - Connects the app container to the shared internal network.
- `depends_on: db`
  - Starts `db` before `app` during compose startup order.
  - Note: this controls startup order, not database readiness.
- `develop.watch` with `action: sync` (`path: .` → `target: /app`)
  - Syncs local source changes into the running `app` container during development.
  - Reduces rebuild/restart cycles for code-only changes and speeds up feedback.
  - Keeps container runtime close to production while preserving local dev convenience.

## Service: db

- `image: postgres:18.3-alpine3.23`
  - Runs PostgreSQL from the official Docker Hub image tag.
- `environment`
  - Reads DB credentials/config from environment variables:
    - `POSTGRES_USER`
    - `POSTGRES_PASSWORD`
    - `POSTGRES_DB`
- `volumes: pg-data:/var/lib/postgresql/data`
  - Persists PostgreSQL data on a named Docker volume.
- `networks: app-network`
  - Places the DB on the same network as `app` for internal communication.

## Named volume

- `pg-data`
  - Created and managed by Docker.
  - Keeps DB data even if containers are removed.

## Volumes Deep Dive (important)

- In `db` service, this line:
  - `pg-data:/var/lib/postgresql/data`
- Means:
  - Left side (`pg-data`) is a **named Docker volume**.
  - Right side (`/var/lib/postgresql/data`) is the path **inside the Postgres container** where DB files are stored.

### Does Docker store this on your local system?

- Yes. Docker stores named-volume data on your local machine, but Docker manages the exact host path.
- In Docker Desktop, that path is inside Docker's internal VM/backend storage, not usually a normal project folder.
- So conceptually: data is local, persistent, and managed by Docker.

### What persists vs what gets deleted

- `docker compose down`
  - Removes containers and network.
  - Keeps `pg-data` volume (your DB data stays).
- `docker compose down -v`
  - Removes containers, network, **and** named volumes.
  - `pg-data` is deleted (DB data lost).

### How to inspect the real host location

```bash
docker volume ls
docker volume inspect <your_project_name>_pg-data
```

- Compose often prefixes volume name with project name, e.g. `fastapi-postgres-docker_pg-data`.
- `docker volume inspect` shows the mountpoint path managed by Docker.

### Named volume vs bind mount (quick distinction)

- Named volume (`pg-data:/var/lib/postgresql/data`)
  - Best for DB persistence, portable across machines, Docker-managed.
- Bind mount (`./some-folder:/var/lib/postgresql/data`)
  - Uses an explicit host folder you choose.
  - More direct host access, but easier to break permissions/cross-OS behavior for databases.

## Named network

- `app-network`
  - Private bridge network for service-to-service traffic.
  - The app can typically reach database using host name `db` inside this network.

## How to run

```bash
docker compose up --build
```

Then open:

- App: `http://localhost:8000`
- API docs: `http://localhost:8000/docs`

To stop:

```bash
docker compose down
```

To stop and remove DB data volume too:

```bash
docker compose down -v
```
