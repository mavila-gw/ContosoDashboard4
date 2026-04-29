---
description: "Create or update the project constitution and deploy stack rules to the active AI agent"
agent: "starterkit.constitution"
---

Run the `starterkit.constitution` agent.


## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

Supported parameters:
- `stack=<name>` — stack identifier for deploying matching rules (e.g. `stack=angular-python`). If omitted, inferred from codebase analysis or asked interactively.

## Pre-Execution Checks

**Check for extension hooks (before constitution update)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_constitution` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:

> Invoke via the `starterkit.constitution` agent for full functionality.
