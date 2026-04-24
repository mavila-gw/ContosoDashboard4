---
description: "Python backend layout, configuration, and dependency injection"
globs: "**/*.py,**/pyproject.toml,**/Dockerfile,**/.env.example"
alwaysApply: false
---

# Python Backend Patterns

## Layout (adapt to repo)

Typical layers:

- **`app/api/` or `routers/`** — HTTP routes only.
- **`app/services/` or `domain/`** — business logic, testable without HTTP.
- **`app/models/`** — ORM models; separate **Pydantic** schemas for API boundaries when used.
- **`app/db/`** — session factory, engine, migration env.
- **`app/core/`** — settings (`pydantic-settings`), security helpers.

## Configuration

- **Environment variables** or `.env` for secrets in dev; **never** commit secrets.
- Strongly typed settings object; fail fast on missing required vars in production.

## Dependency injection

- FastAPI: `Depends()` for DB sessions and services.
- Avoid **global mutable singletons** for request-scoped resources.

## Async

- Prefer **async** route handlers and DB drivers when the stack supports it consistently.
- Do not block the event loop with long CPU-bound work — offload to workers when needed.

## Migrations

- **Alembic** (or project tool) for schema changes; one migration per logical change when possible.
- Review migrations for **locking** and **downtime** on large tables.
