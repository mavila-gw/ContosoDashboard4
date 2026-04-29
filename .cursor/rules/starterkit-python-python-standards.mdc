---
description: "Python coding standards — type hints, naming, error handling, and testing"
globs: "**/*.py"
alwaysApply: false
---

# Python Standards

## Style & typing

- Follow **`ruff`** / **`black`** / project `pyproject.toml` (line length, import order).
- Use **type hints** on public functions and service methods; narrow types at boundaries (`str | None` vs bare `Any`).

## Naming

- **`snake_case`** for functions, variables, modules.
- **`PascalCase`** for classes.
- **`UPPER_SNAKE`** for module-level constants.

## Errors

- Raise **specific exceptions**; catch at boundaries (HTTP layer, job runner).
- Log exceptions with **structured** context; never swallow errors silently.

```python
# Avoid
try:
    save_user(user)
except Exception:
    pass

# Prefer
try:
    save_user(user)
except StorageError as e:
    logger.exception("user_save_failed", extra={"user_id": user.id})
    raise HTTPException(status_code=503, detail="Unavailable") from e
```

## Tests

- **`pytest`** with clear arrange/act/assert structure.
- Use **fixtures** for DB and API clients; isolate tests from production data.
