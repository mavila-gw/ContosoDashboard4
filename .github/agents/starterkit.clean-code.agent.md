---
description: "Identify and remove AI-generated slop, placeholder names, dead code, and style inconsistencies"
---


# starterkit.clean-code

Scans target files for AI-generated slop and code quality issues: placeholder names, redundant comments, dead code, and style inconsistencies. Fixes each match and formats per project conventions defined in `.starterkit/memory/constitution.md`.

## Parameters

```
<file-or-glob>    File or glob pattern to scan (e.g. src/api/views.py, src/**/*.ts).
                  If omitted, scans the current file or recently edited files.
```

## User Input

```text
$ARGUMENTS
```

## Default prompt

# Clean Code

When cleaning code in this project, apply the checklist below and fix any matches. Consult `.starterkit/memory/constitution.md` for the project's configured linters and formatters (e.g. Prettier, ESLint, Ruff, Black, StyleCop) and prefer those when available.

## Slop Patterns to Remove

### Naming

- **Placeholder names**: `temp`, `data`, `data2`, `result`, `result_final`, `foo`, `bar`, `baz`, `test_var`, `test123` → Replace with descriptive names.
- **Generic handlers**: `handle_it`, `do_stuff`, `process_data` → Split or rename to reflect actual behavior.

### Comments

- **Remove**: Obvious comments that restate the code (`// Increment counter` above `counter++`).
- **Remove**: Entire blocks of commented-out code.
- **Resolve or remove**: `TODO`, `FIXME`, `HACK`, `XXX` — either implement or delete.
- **Trim**: Inflated docstrings with buzzwords; keep only useful documentation.

### Dead Code

- Unused imports and variables.
- Unreachable code after early returns.
- Redundant null checks that add no value.
- Empty `catch`/`except`/`finally` blocks with only a no-op — handle or rethrow.

### Redundancy

- Overly defensive checks (e.g., null checks before operations that already guard).
- Duplicated logic — extract to a single function.
- Unnecessary intermediate variables used only once.

### Style Consistency

- Match project conventions: naming, brace placement, spacing (per constitution).
- One statement per line where readable.
- Remove trailing whitespace and fix inconsistent indentation.

## Workflow

1. **Scan** the target file(s) for the patterns above.
2. **Fix** each match: rename, delete, simplify, or extract.
3. **Verify** no behavior change; run tests if available.
4. **Format** per project style (linter/formatter configured in constitution).

## Examples

**Before (slop):**
```
getData() {
  const x = this.http.get('/api/x'); // TODO
  return x;
}
```

**After (clean):**
```
loadUserProfile(): Observable<UserProfile> {
  return this.http.get<UserProfile>('/api/me');
}
```

**Before (slop):**
```python
try:
    parse_config()
except:
    pass  # TODO: handle errors
```

**After (clean):**
```python
try:
    parse_config()
except ConfigError as e:
    logger.warning("Config parse failed, using defaults", exc_info=e)
```

## Anti-Patterns

- Do not add comments to explain what was removed.
- Do not over-simplify to the point of obscuring intent.
- Preserve meaningful error handling and edge-case logic.
