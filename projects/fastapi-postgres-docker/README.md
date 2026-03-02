# fastapi-postgres-docker

A project created with FastAPI CLI. - `uvx fastapi-new` - [docs](https://github.com/fastapi/fastapi-new)

## Quick Start

### Start the development server

```bash
uv run fastapi dev
```

Visit http://localhost:8000

### Deploy to FastAPI Cloud

> FastAPI Cloud is currently in private beta. Join the waitlist at https://fastapicloud.com

```bash
uv run fastapi login
uv run fastapi deploy
```

## Project Structure

- `main.py` - Your FastAPI application
- `pyproject.toml` - Project dependencies

## Learn More

- [FastAPI Documentation](https://fastapi.tiangolo.com)
- [FastAPI Cloud](https://fastapicloud.com)

## Project Notes

- To manage environment variables - we will use [pydantic-settings](https://docs.pydantic.dev/latest/concepts/pydantic_settings/) and let's install it.
- Also install [asyncpg](https://github.com/MagicStack/asyncpg) to connect to postgreSQL - `asyncpg` is a database interface library designed specifically for PostgreSQL and Python/asyncio
- Also install [`advanced-alchemy`](https://github.com/litestar-org/advanced-alchemy) - Advanced Alchemy includes a set of asynchronous and synchronous repository classes for easy CRUD operations on your SQLAlchemy models.
  - `uv add pydantic-settings asyncpg advanced-alchemy[cli]`
- also we run `uv lock` after installing above ones - because i want to have uv lock to have the specific dependencies and that will be useful when we are containarizing the entire setup.
