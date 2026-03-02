# docker snippets notes

## Start project fresh

docker compose up --build

## Rebuild single service

docker compose up --build app

## Run migrations inside container

docker compose exec app alembic upgrade head

## Check what's running

docker ps

## See logs of one service

docker compose logs -f app

## Initialize git submodules

git submodule update --init --recursive

## Cleanup unused Docker resources

docker system prune

### Why we use it

- Removes unused Docker objects (stopped containers, unused networks, dangling images, and build cache).
- Frees disk space and keeps your local Docker environment clean.
- Helps avoid confusion from old containers/images while troubleshooting.

### Why it's important

- Docker files can grow quickly over time and consume significant storage.
- Cleanup can improve local dev performance and reduce "no space left on device" issues.
- Keeps only resources that are actively used by running containers.

### When to use it

- After finishing an experiment/feature branch with many rebuilds.
- When disk usage is high.
- When you want a clean local environment before retesting.

### Common commands

- Basic cleanup (safe default):
  docker system prune
- Include unused (not just dangling) images:
  docker system prune -a
- Also remove unused volumes (use carefully):
  docker system prune --volumes

### Tip before prune

- Check Docker disk usage first:
  docker system df
