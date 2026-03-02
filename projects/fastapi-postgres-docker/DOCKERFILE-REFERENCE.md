# Dockerfile Reference

This file explains what the project Dockerfile is doing and what each instruction contains.

## Current Dockerfile

```dockerfile
FROM ghcr.io/astral-sh/uv:debian

WORKDIR /app

COPY pyproject.toml uv.lock ./

RUN uv sync --locked

COPY . .

EXPOSE 8000

CMD ["uv", "run", "fastapi", "dev", "--host", "0.0.0.0", "--port", "8000"]
```

## Line-by-line Explanation

- `FROM ghcr.io/astral-sh/uv:debian`
  - Base container image with `uv` preinstalled on Debian.
- `WORKDIR /app`
  - Sets `/app` as the working directory for all next instructions.
- `COPY pyproject.toml uv.lock ./`
  - Copies only dependency files first to improve Docker cache usage.
- `RUN uv sync --locked`
  - Installs dependencies exactly from `uv.lock`.
- `COPY . .`
  - Copies source code and remaining project files into `/app`.
- `EXPOSE 8000`
  - Documents that the containerized app listens on port `8000`.
- `CMD ["uv", "run", "fastapi", "dev", "--host", "0.0.0.0", "--port", "8000"]`
  - Default runtime command that starts your FastAPI app and binds to all interfaces on port `8000`.

## Why this order matters

- Copying `pyproject.toml` and `uv.lock` before app code allows Docker to reuse dependency layers when only source code changes.
- Using `uv sync --locked` keeps builds reproducible.
- Running with `--host 0.0.0.0` makes the app reachable from outside the container (for mapped ports).
