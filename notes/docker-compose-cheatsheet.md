# Docker Compose - Running Notes

## Why Docker Compose Exists

A FastAPI app needs PostgreSQL to work. That means two containers:

- One running the FastAPI app
- One running PostgreSQL

You _could_ start them manually with two separate `docker run` commands,
but you'd have to manually handle how they connect, what order they start in,
and shared config. Docker Compose handles all of this in one file.

> **One command → multiple containers, all wired together correctly.**

---

## The `compose.yaml` File

Describes your entire multi-container setup in one place.
When you run `docker compose up`, Docker reads this file and brings everything up together.

---

## The Three Main Sections

### `services`

The containers you want to run together.
Each service becomes its own container with its own config.

```yaml
services:
  app: # our FastAPI container
    build: . # build from our Dockerfile
    ports:
      - "8000:8000"

  db: # our PostgreSQL container
    image: postgres:15 # pull this ready-made image from Docker Hub
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
```

**Key point:** Service names (`app`, `db`) become the **hostnames** containers use
to talk to each other. Your FastAPI app connects to PostgreSQL using `db` as the host:

```
DATABASE_URL = "postgresql://myuser:mypassword@db:5432/mydb"
#                                               ↑
#                               service name becomes the hostname
```

This is why you never use `localhost` inside Docker Compose — `localhost` inside
a container means _that container itself_, not the database container.

---

### `volumes`

Solves the data persistence problem.

By default, when a container stops, **everything inside it is gone** — including your
database data. Volumes tell Docker to store certain data on your actual physical disk
_outside_ the container so it survives restarts.

```yaml
volumes:
  postgres_data: # define a named volume

services:
  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
      #  ↑ named volume    ↑ path inside the container where postgres stores data
```

Two types of volumes worth knowing:
| Type | What it does | When to use |
|------|-------------|-------------|
| **Named volume** (`postgres_data:/path`) | Docker manages storage location | Database data you want persisted |
| **Bind mount** (`./app:/app`) | Maps a local folder into the container | Dev hot-reload — so code changes reflect instantly without rebuilding |

---

### `networks`

Controls how containers talk to each other.

By default, Docker Compose automatically creates a network and puts all services on it —
so in simple projects you often don't need to define this manually.
But when you do define it explicitly, you get more control.

```yaml
networks:
  backend_network:
    driver: bridge # standard network type for container-to-container communication
```

**Bridge network** = an internal virtual network. Containers on it can reach each other
by service name, but the outside world can only reach them through exposed ports.

---

## The `depends_on` — Start Order

Your FastAPI app will crash if it tries to connect to Postgres before Postgres is ready.
`depends_on` tells Compose to start the `db` container before the `app` container.

```yaml
services:
  app:
    build: .
    depends_on:
      - db # don't start app until db container is running
```

> **Gotcha:** `depends_on` only waits for the container to _start_, not for PostgreSQL
> inside it to be _ready to accept connections_. For production you need a healthcheck.

---

## The Full Picture — How It All Connects

```
docker compose up
        ↓
Docker reads compose.yaml
        ↓
Starts db container (PostgreSQL on Debian Linux)
  — data stored in named volume on your physical disk
        ↓
Starts app container (FastAPI on uv:debian)
  — connects to db using hostname "db" on port 5432
  — your local code mounted in via bind mount (hot reload)
        ↓
You hit localhost:8000 from your browser
Docker routes it into the app container
App talks to db container over the internal bridge network
```

---

## Key Commands

```bash
# Start everything (builds images if needed)
docker compose up

# Start and force rebuild of images
docker compose up --build

# Start in background (detached mode)
docker compose up -d

# Stop everything
docker compose down

# Stop and delete volumes (wipes database data)
docker compose down -v

# See logs of all services
docker compose logs -f

# See logs of one service only
docker compose logs -f app

# Run a command inside a running container
docker compose exec app bash
docker compose exec app uv run alembic upgrade head
```

---

## `develop.watch` Sync vs Rebuild

- Use `develop.watch` with `action: sync` for **code-only changes** (fast feedback, no full image rebuild).
- Use `docker compose up --build` when you change **Dockerfile, dependencies, system packages, or build-time config**.
- Rule of thumb: if runtime files changed, sync is enough; if image layers changed, rebuild.

---

## Things to Never Get Wrong

1. **Never use `localhost` as DB host inside Docker** — use the service name (`db`)
2. **Never store secrets in compose.yaml directly** — use a `.env` file and reference with `${VAR_NAME}`
3. **Always add `.env` to `.gitignore`** — never commit passwords to git
4. **Always define a volume for PostgreSQL** — otherwise every `docker compose down` wipes your data
