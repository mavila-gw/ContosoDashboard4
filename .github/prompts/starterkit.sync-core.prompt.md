---
description: "Toolkit maintenance command: clean and adapt a legacy cursor file (agent, rule, or template) into a stack-agnostic, agent-agnostic equivalent under starterkit/."
agent: "starterkit.sync-core"
---

Run the `starterkit.sync-core` agent.


# starterkit.sync-core

**Toolkit maintenance tool.** Not part of the SDD user workflow.
Synchronises `starterkit/` from the legacy Cursor kit when the legacy evolves.

> **Who runs this:** the starterkit maintainer — not end users.
> **When:** after detecting relevant changes in the legacy under `cursor/`.

## Parameters

```
--source <path>   Path to the legacy file to adapt (relative to repo root or absolute).
--name <name>     Base name for the output file (no extension).
--type <type>     One of: core | rule | template  (default: core)
                    core     → writes to starterkit/cores/<name>.md
                    rule     → writes to starterkit/rules/<subfolder>/<name>.md
                              (subfolder derived from content: common | angular | python | dotnet | blazor | razor)
                    template → writes to starterkit/templates/<name>.md
```

> Invoke via the `starterkit.sync-core` agent for full functionality.
