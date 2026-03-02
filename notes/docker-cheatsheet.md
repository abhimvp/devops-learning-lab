# notes on docker

- reference

## Why Docker Compose exists

When you have multiple containers (app + db), you need them to:

- start in the right order
- talk to each other on an internal network
- share environment variables
  docker-compose handles all of this so you don't run 5 docker commands manually.

## What `pip install -e .` actually means

Editable install — links the package from source instead of copying it.
Means you can edit code without reinstalling. REQUIRES C extensions to compile.
If you see "No such file .c" — check git submodules were initialized first.
