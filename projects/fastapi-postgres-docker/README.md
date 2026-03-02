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
- Now have the task of containerizing our simple fast API application
  - create `Dockerfile` in the root project directory
  - since we're using `uv` - we will be making use of `uv image` in docker - uv project provides a [image](https://docs.astral.sh/uv/guides/integration/docker/) for docker - refer this [guide](https://docs.astral.sh/uv/getting-started/installation/#docker).

## Dockerfile Notes

- `FROM ghcr.io/astral-sh/uv:debian`: uses the uv-enabled Debian base image.
- `WORKDIR /app`: sets the working directory inside the container.
- `COPY pyproject.toml uv.lock ./`: copies dependency metadata first for better Docker layer caching.
- `RUN uv sync --locked`: installs exact locked dependencies.
- `COPY . .`: copies the rest of application code.
- `EXPOSE 8000`: declares the app port.
- `CMD ["uv", "run", "fastapi", "dev", "--host", "0.0.0.0", "--port", "8000"]`: starts the FastAPI app on all interfaces at port 8000.
