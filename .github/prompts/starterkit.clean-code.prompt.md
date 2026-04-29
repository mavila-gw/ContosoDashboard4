---
description: "Identify and remove AI-generated slop, placeholder names, dead code, and style inconsistencies"
agent: "starterkit.clean-code"
---

Run the `starterkit.clean-code` agent.


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


> Invoke via the `starterkit.clean-code` agent for full functionality.
