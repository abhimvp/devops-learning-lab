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
