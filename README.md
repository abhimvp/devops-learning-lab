# DevOps Learning Lab

My hands-on learning repo for Docker, Kubernetes, and backend DevOps.
Started: March 2nd, 2026

## Projects

| #   | Project                       | Stack                                        | Status         | Key Learnings                                |
| --- | ----------------------------- | -------------------------------------------- | -------------- | -------------------------------------------- |
| 01  | FastAPI + PostgreSQL + Docker | FastAPI, SQLAlchemy, Alembic, Docker Compose | 🔄 In Progress | docker-compose networking, editable installs |

## Notes

- [Docker Cheatsheet](./notes/docker-cheatsheet.md)
- [Docker Mistakes Log](./notes/docker-mistakes.md)
- [Docker Snippets](./notes/docker-snippets.md)

## What I'm Learning

Currently following: Ssali Jonathan's FastAPI + Docker series - [#1](https://www.youtube.com/live/MiW_O9e5FO0?si=uCDwjkhk2EzkWQPe) and [#2](https://www.youtube.com/live/NOZzoYqrDLg?si=AE3e9H5ziwDvB53D)

## Repo Structure - devops-learning-lab

```
devops-learning-lab/
│
├── README.md                          ← your learning dashboard
│
├── notes/
│   ├── docker-cheatsheet.md           ← key concepts & mental models
│   ├── docker-mistakes.md             ← what broke & how you fixed it
│   ├── docker-snippets.md             ← commands & code to copy-paste
│   ├── kubernetes-cheatsheet.md
│   └── kubernetes-snippets.md
│
├── projects/
│   │
│   ├── 01-fastapi-postgres-docker/    ← Ssali Jonathan's series
│   │   ├── README.md                  ← what this project is, what you learned
│   │   ├── app/                       ← actual code from the tutorial
│   │   └── mistakes.md                ← project-specific issues you hit
│   │
│   └── 02-next-project/               ← next thing you build
│       └── ...
│
└── assessments/
    └── 2025-11-aiohttp-log-analysis.md  ← log file breakdown
```

## Folder Purpose

| Folder         | What goes in it                                                                                          |
| -------------- | -------------------------------------------------------------------------------------------------------- |
| `notes/`       | Technology-level notes — not tied to any one project. Cheatsheets, snippets, mistakes that apply broadly |
| `projects/`    | One folder per tutorial or project you build. Numbered so the progression is clear                       |
| `assessments/` | Real problems you've encountered — interview tasks, log analysis, debugging sessions                     |

## Notes File Convention

Each technology gets 3 files:

- **`cheatsheet.md`** — concepts & mental models, written like you're explaining to yourself
- **`mistakes.md`** — what broke, why, and how you fixed it
- **`snippets.md`** — commands and code blocks, no explanation needed, just copy-paste ready

## Project README Template

Each project folder should have a `README.md` with:

```markdown
## What This Is

[1-2 lines on what the project does]

## Stack

[tools used]

## What I Learned

[bullet points — fill this in as you go]

## Mistakes & Fixes

[link to mistakes.md or inline]
```
