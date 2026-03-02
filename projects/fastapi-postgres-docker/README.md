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
- Now to make this work with postgresql - we are going to make use of [docker compose](https://docs.docker.com/compose/). Docker compose allows you to run multiple containers and set up configurations on how they will work together and how they will communicate to each other & rest of configurations to make them work each other.
  - create a `compose.yaml` file - inside it we will describe the different services we shall have within our setup.
  - different sections in it:
    - `services` - different containers you want to run together like application(fastapi app) , database(postgreSQL container) ..etc
    - `volumes` - ways to store data - docker utilizes our physical disk to store things outside the container - such that when the container is not running - data is persisted
    - `networks` - specifies how these containers talk to each other. so we create a simple network and then we shall have our database having an IP and a host which we shall connect to from our application ..etc
- get the postgres docker images from [this reference](https://hub.docker.com/_/postgres)
- Volume behavior in this setup:
  - `pg-data:/var/lib/postgresql/data` is a Docker **named volume**.
  - Data is persisted on your local machine in Docker-managed storage.
  - `docker compose down` keeps DB data; `docker compose down -v` deletes it.
- now setup `.env` file for the fastapi project to make use of environment variables and all - see the variables in .env in `.env.example` file.
- To read the environment variables from python - we will do following setup:
  - create `src` folder
    - create `__init__.py` file
    - create `config.py` file - here we setup pydantic-setting - which can read environment variables from env files

```bash
# when we do - docker compose up - we see this if we everything goes well
 ✔ Image postgres:18.3-alpine3.23              Pulled
 ✔ Image fastapi-postgres-docker-app           Built
 ✔ Network fastapi-postgres-docker_app-network Created
 ✔ Volume fastapi-postgres-docker_pg-data      Created
 ✔ Container fastapi-postgres-docker-db-1      Created
 ✔ Container fastapi-postgres-docker-app-1     Created
```

- also add `docker compose watch` allows us to preview changes we make to our containers or code in it in real time - useful in dev mode and add the instructions for it in compose.yaml file. This will also us to reload our fastapi server when we make changes.

## Dockerfile Notes

- `FROM ghcr.io/astral-sh/uv:debian`: uses the uv-enabled Debian base image.
- `WORKDIR /app`: sets the working directory inside the container.
- `COPY pyproject.toml uv.lock ./`: copies dependency metadata first for better Docker layer caching.
- `RUN uv sync --locked`: installs exact locked dependencies.
- `COPY . .`: copies the rest of application code.
- `EXPOSE 8000`: declares the app port.
- `CMD ["uv", "run", "fastapi", "dev", "--host", "0.0.0.0", "--port", "8000"]`: starts the FastAPI app on all interfaces at port 8000.

## Container References

- Dockerfile explanation: [DOCKERFILE-REFERENCE.md](./DOCKERFILE-REFERENCE.md)
- Compose explanation: [COMPOSE-REFERENCE.md](./COMPOSE-REFERENCE.md)
