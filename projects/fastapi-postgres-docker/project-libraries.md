# Project Libraries — FastAPI + PostgreSQL + Docker

- prompt to agent to update this doc about new libraries or anything used in this project

```md
I'm following a tutorial and just installed new libraries. Add them to my existing project-libraries.md in the same table format.
Newly installed: <paste the install command here>
Context: <one line on what part of the project this is for — e.g. "setting up auth" or "adding migrations">
Here's my current file: <paste the file contents>
```

## What `uvx fastapi-new` Pre-Installs

| Library      | What it is              | Why it's there                                                                  |
| ------------ | ----------------------- | ------------------------------------------------------------------------------- |
| **FastAPI**  | The web framework       | Handles HTTP routes, request/response, auto-generates API docs at `/docs`       |
| **uvicorn**  | ASGI server             | Actually runs the FastAPI app — like a waiter that serves incoming requests     |
| **pydantic** | Data validation library | FastAPI uses this under the hood to validate request bodies and response shapes |

---

## What `uv add pydantic-settings asyncpg advanced-alchemy` Adds

| Library               | What it is                   | Why it's there                                                                                                                                            |
| --------------------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **pydantic-settings** | Settings/config manager      | Reads env variables (DB URL, secrets) and validates them at startup — if something's missing, the app fails loudly instead of breaking silently later     |
| **asyncpg**           | Async PostgreSQL driver      | The actual low-level connector that speaks to Postgres — async means it doesn't block while waiting for DB responses, keeping FastAPI performant          |
| **advanced-alchemy**  | SQLAlchemy companion library | Pre-built repository patterns, smart base model classes, and Alembic (migrations) wired up for async — reduces boilerplate you'd otherwise write yourself |

> **Note:** `advanced-alchemy` pulls in **SQLAlchemy** and **Alembic** as dependencies automatically.
> You don't need to install them separately.

---

## How They All Connect

```
HTTP Request
     ↓
FastAPI              ← handles routing, request parsing
     ↓
pydantic-settings    ← app reads DB_URL, secrets from env vars
     ↓
advanced-alchemy     ← repository pattern, model base classes, migrations
     ↓
SQLAlchemy           ← ORM, query building (pulled in by advanced-alchemy)
     ↓
asyncpg              ← actual async connection to Postgres
     ↓
PostgreSQL           ← running in a Docker container
```

---

## uv vs uvx — Key Difference

| Command            | What it does                                                            | Equivalent to    |
| ------------------ | ----------------------------------------------------------------------- | ---------------- |
| `uv add <package>` | Adds library to your project permanently, saved to `pyproject.toml`     | `pip install`    |
| `uvx <tool>`       | Runs a one-off tool temporarily without installing it into your project | `npx` in Node.js |

So `uvx fastapi-new` ran the scaffolding tool temporarily,
while `uv add pydantic-settings asyncpg advanced-alchemy` permanently added those 3 to the project.

---

## pydantic-settings — Why It Matters for Docker

This is how secrets flow safely from `docker-compose.yml` into your app.
Env vars are injected at runtime, never hardcoded or baked into the image.

```python
# Bad — hardcoded, never do this
DATABASE_URL = "postgresql://user:password@localhost/db"

# Bad — no validation, could be None and silently break
import os
DB_URL = os.getenv("DATABASE_URL")

# Good — validated, typed, fails loudly if missing
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str   # required — app won't start if this is missing
    debug: bool = False # optional — defaults to False
```

---

## asyncpg — Why Async Matters

Regular `psycopg2` (the old Postgres driver) is **synchronous** — it blocks the entire app
while waiting for a DB query to finish.

`asyncpg` is **async** — while waiting for Postgres to respond,
FastAPI can handle other incoming requests in the meantime.

```
FastAPI (async) → SQLAlchemy (async) → asyncpg (async) → PostgreSQL
```

> If any link in that chain is synchronous, you lose the performance benefit.
> The whole stack needs to be async end-to-end.
