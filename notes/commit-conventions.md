# Commit Message Conventions

Follow this pattern for every commit:

```txt
<type>: <short description>
```

## Types

| Type     | When to use                                |
| -------- | ------------------------------------------ |
| `init:`  | First commit, setting up structure         |
| `docs:`  | Adding/updating notes, README, cheatsheets |
| `feat:`  | New working feature in a project           |
| `fix:`   | Something was broken, now it works         |
| `chore:` | Cleanup, reorganizing files                |

## Example History

```txt
init: project structure and learning notes setup
docs: add docker cheatsheet and snippets from log analysis session
feat: fastapi app running with docker compose
fix: postgres connection failing due to wrong env variable
docs: update mistakes log with postgres networking issue
feat: alembic migrations working inside container
```

> Your commit history becomes a learning journal you can look back on.
> Way more useful than just code sitting there with no context.
