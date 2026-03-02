# Docker — The Right Mental Model

## Why Docker Exists

Before Docker, the classic problem was **"it works on my machine"** — your app runs fine
locally but breaks in production because the OS, Python version, or system libraries are different.

Docker fixes this by packaging your **app + its entire OS environment** together into one unit
that runs identically everywhere — your laptop, a teammate's machine, AWS, CI pipelines.

---

## Image vs Container — The Most Important Distinction

|                | Image                         | Container                                 |
| -------------- | ----------------------------- | ----------------------------------------- |
| **What it is** | A blueprint / recipe (static) | A live running instance of that blueprint |
| **Analogy**    | A recipe for a dish           | The actual dish made from that recipe     |
| **Created by** | `docker build`                | `docker run`                              |
| **How many**   | One image                     | Many containers from the same image       |

> You never "run an image" — you run a **container** based on an image.
> The image itself just sits there as a blueprint.

---

## Why Linux (Debian) — Even on Windows

Docker Desktop on Windows runs a **hidden Linux VM** underneath.
Every container runs inside that Linux VM — not on Windows directly.

This is why your Dockerfile starts from a Linux base image like Debian.
Your FastAPI app ends up running on Linux regardless of your host OS.

```
Your Windows Machine
└── Docker Desktop (runs a hidden Linux VM)
    └── Container (Debian Linux)
        └── Your FastAPI app running on Linux
```

This is also why apps built with Docker run identically in the cloud —
cloud servers run Linux too, so the environment matches perfectly.

---

## What a Base Image Actually Is

A base image is a **pre-built starting point** for your container.
Instead of setting up Linux from scratch, you start from an image
that already has what you need.

### The uv images explained:

| Image                              | What it contains                         | Use it for                                                 |
| ---------------------------------- | ---------------------------------------- | ---------------------------------------------------------- |
| `ghcr.io/astral-sh/uv:latest`      | **Only the uv binary** — no OS, no shell | Copying the uv binary INTO another image via `COPY --from` |
| `ghcr.io/astral-sh/uv:debian`      | Full Debian Linux OS + uv pre-installed  | Dev environments where you need a shell and build tools    |
| `ghcr.io/astral-sh/uv:debian-slim` | Stripped-down Debian + uv                | Lighter prod images                                        |
| `ghcr.io/astral-sh/uv:alpine`      | Tiny Alpine Linux + uv                   | Smallest possible prod images                              |

### Why Ssali used `uv:debian` and not `uv:latest`

`uv:latest` is **distroless** — it contains nothing but the uv binary.
No shell, no apt-get, no build tools. You can't actually run a server in it.

`uv:debian` is a full OS with uv pre-installed, so you can:

- Run a shell inside the container to debug
- Use `apt-get` to install system packages
- Compile Python packages that have C extensions (like `asyncpg`)
- Actually run your FastAPI server

For a **dev environment**, debian is the right choice. Practical over minimal.

---

## The Sequence — How It All Fits Together

```
Your Code  (FastAPI app on Windows)
      ↓
Dockerfile
  — FROM ghcr.io/astral-sh/uv:debian   ← start with Debian Linux + uv
  — COPY . /app                         ← copy your code into the image
  — RUN uv sync                         ← install Python dependencies
  — CMD ["uv", "run", "fastapi", ...]   ← tell it how to start the app
      ↓
docker build   →   IMAGE created (the blueprint)
      ↓
docker run     →   CONTAINER starts (live Debian Linux environment)
      ↓
FastAPI app running on Linux
— works the same on your machine, teammate's machine, AWS, anywhere
```

---

## Key Terminology

| Term               | Plain English                                                                           |
| ------------------ | --------------------------------------------------------------------------------------- |
| **Image**          | Frozen blueprint of an OS + your app. Built once, used many times                       |
| **Container**      | A live running environment created from an image                                        |
| **Base image**     | The starting point image your Dockerfile builds on top of                               |
| **Distroless**     | An image with no OS — just a single binary. Tiny and secure but can't run interactively |
| **Derived image**  | An image built on top of a real OS (like Debian or Alpine)                              |
| **`docker build`** | Reads your Dockerfile and creates an image                                              |
| **`docker run`**   | Creates and starts a container from an image                                            |
| **`COPY --from`**  | Multi-stage build trick — copy files out of one image into another                      |

---

## Production Tip — Pin Your Image Versions

In tutorials, floating tags like `uv:debian` are fine.
In production, always pin to a specific version:

```dockerfile
# Risky in production — tag moves as uv updates
FROM ghcr.io/astral-sh/uv:debian

# Safe — always builds with the exact same uv version
FROM ghcr.io/astral-sh/uv:0.10.7-debian
```

Pinning = reproducible builds. The same Dockerfile always produces the same image.
