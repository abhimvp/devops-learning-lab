# docker-mistakes - learnings

## [2025-11] Docker build: mask.c No such file or directory

Ran `pip install -e .` before `git submodule update --init`
C source files didn't exist yet → compiler failed
FIX: always run submodule init BEFORE any pip install

## [2025-11] pip install -e . failing silently

Python version was 3.13, project only supported 3.11
Build backend couldn't prepare metadata → cryptic error
FIX: check pyproject.toml for python_requires before choosing base image
