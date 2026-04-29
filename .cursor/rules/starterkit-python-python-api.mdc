---
description: "Python HTTP API conventions (FastAPI preferred; REST status codes and validation)"
globs: "**/*.py,**/pyproject.toml,**/requirements*.txt"
alwaysApply: false
---

# Python API Conventions

Assume **FastAPI** unless the repo uses Flask/Django REST — match existing framework.

## Router design

- Keep **route handlers thin**; delegate to services/use-cases.
- Use **explicit** path operations, tags, and `response_model` for stable contracts.
- Prefer **dependency injection** (`Depends`) for DB sessions, auth, and settings.

## HTTP verbs & status codes

- `GET` → **200** with body, or **404** when a single resource is missing.
- `POST` → **201 Created** with `Location` when creating a resource; **409** for conflicts if applicable.
- `PUT`/`PATCH` → **200** or **204**.
- `DELETE` → **204** or **404**.

## Validation & schemas

- **Pydantic** models for request/response; validate at the boundary.
- Version or extend schemas for **backward-compatible** changes; document breaking changes in OpenAPI and release notes.

## Errors

- Return **consistent** error bodies (e.g. problem+json shape if the project uses it).
- Map unexpected failures to appropriate **5xx**; never leak stack traces in production responses.

## OpenAPI

- Keep **OpenAPI** spec aligned with live behavior (see `sdd-principles` rule).
