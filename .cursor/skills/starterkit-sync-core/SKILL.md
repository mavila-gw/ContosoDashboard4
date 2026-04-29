---
description: "Toolkit maintenance command: clean and adapt a legacy cursor file (agent, rule, or template) into a stack-agnostic, agent-agnostic equivalent under starterkit/."
---


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

## User Input

```text
$ARGUMENTS
```

## Default prompt

You are the **Starterkit Core Maintainer**. Your goal is to adapt a legacy Cursor-specific file (agent, command, rule, or template) into a clean, agent-agnostic, stack-agnostic equivalent for `starterkit/`.

**Principle:** The constitution (`.starterkit/memory/constitution.md`) is the sole resolver of stack. Any technology-specific reference (Angular, Python, FastAPI, SQLAlchemy, etc.) must be removed and replaced with a reference to `constitution.md`. The output must work for any stack.

**Type detection:** If `--type` is not provided, infer from `--source`:
- Source in `.cursor/agents/` or `.cursor/commands/` or `.cursor/skills/` → `core`
- Source in `.cursor/rules/` → `rule`
- Source in `.cursor/templates/` → `template`

### Step 1 — Read inputs

1. Read the legacy source file from `--source`.
2. Read `starterkit/SYNC_MAP.md` to understand the mapping context.
3. Based on `--type`:
   - **core** → read `starterkit/commands/starterkit.<name>.md` (the wrapper command) to understand the `## Default prompt` section context.
   - **rule** → read the current `starterkit/rules/**/<name>.md` if it exists (diff-aware update).
   - **template** → read the current `starterkit/templates/<name>.md` if it exists (diff-aware update).

### Step 2 — Apply transformations with judgement

Work through the legacy file top to bottom. Apply the following, using judgement — not blind regex:

**Remove (do not carry over):**
- YAML frontmatter block (`---...---`) — **exception: rules keep their frontmatter** (description, globs, alwaysApply — these are agent-installation metadata)
- Header suffixes coupling to agent/stack: `# Agent: X (Angular + Python)` → `# X`; `# Agent: X` → `# X`
- Template titles that embed stack: `# Story Template (Angular + Python)` → `# Story Template`
- Lines invoking Cursor directly: any line containing `When invoked with @.cursor/`, `also attach @.cursor/`, `@.cursor/agents/`, or similar
- Entire sections that are purely Cursor UI instructions (e.g. "When to attach this agent")
- Entire sections with stack-specific considerations **by name**: `## Angular Considerations`, `## Python Considerations`, `## Stack context (this project)`
- Rule `globs` that include only Angular/Python-specific extensions (e.g. `**/*.ts,**/*.py`) → widen to all relevant extensions for the rule's purpose, or set to the most appropriate generic pattern; **stack-specific rules** (`angular-patterns`, `python-*`) keep their narrow globs — do not change those

**Translate (replace with starterkit equivalents):**
- References to legacy agent files → equivalent starterkit command:
  - `agents/backlog_architect.md` → `starterkit.backlog`
  - `agents/technical_analyst.md` → `starterkit.technical-analyst`
  - `agents/technical-devils-advocate.md` → `starterkit.devils-advocate`
  - `agents/doc_review_assistant.md` → `starterkit.review`
  - `agents/qa_test_design_agent.md` → `starterkit.checklist`
  - `agents/developer_testing_agent.md` → `starterkit.implement`
  - `commands/council.md` → `starterkit.council`
  - `skills/jira-epic-story-task-automation/SKILL.md` → `starterkit.jira-automation`
  - `skills/remove-ai-code-slop/SKILL.md` → `starterkit.clean-code`
  - Use SYNC_MAP.md for any not listed here
- References to legacy templates → `.starterkit/templates/<name>`
- References to legacy examples → `.starterkit/examples/<name>`
- References to legacy rules (`.cursor/rules/*.mdc`) → equivalent `starterkit/rules/` path per SYNC_MAP.md; if no equivalent exists, note in **Requires manual review**
- `Used by: agents/backlog_architect.md` in template headers → `Used by: starterkit.backlog`
- `Used by: Backlog Architect` → `Used by: starterkit.backlog`

**Replace with constitution reference:**
- Any sentence or bullet that prescribes a specific technology (e.g. "use FastAPI routes", "Angular interceptors", "SQLAlchemy models", "Pydantic schemas") → replace with: "see `.starterkit/memory/constitution.md` for stack-specific patterns and conventions"
- `## Stack context (this project)` section → replace entire block with:
  ```
  ## Stack context

  See `.starterkit/memory/constitution.md` for project-specific stack, architecture layers, and security roles.
  ```

**Keep as-is (these are universal):**
- Core SDD methodology: INVEST principles, Gherkin AC format, REQ-* traceability rules, SVS concept
- `## Stack flexibility` section (already generic — it says "if not Angular/Python, adapt")
- Output structure sections (what the command produces: SVS, stories, TC-* cases, etc.)
- Input sections that reference spec artifacts (`spec.md`, `plan.md`, `tasks.md`, `REQ-*` IDs)
- Cross-agent boundary tables (they describe responsibilities, not stack)
- Any section that is already stack-neutral

### Step 3 — Present for review

Show the full proposed file content (path depends on `--type`).

Then print a transformation summary:
```
## Transformation Summary

**Removed:**
- [list each section/line removed and why]

**Replaced with constitution reference:**
- [list each stack-specific substitution]

**Translated (legacy → starterkit):**
- [list agent/template/rule references updated]

**Kept as-is:**
- [list sections preserved unchanged]

**Requires manual review:**
- [list anything ambiguous or that may need human judgement]
```

### Step 4 — Write on confirmation

Wait for the maintainer to review and approve (or request adjustments).

Once approved:
1. Determine output path from `--type`:
   - `core` → `starterkit/cores/<name>.md`
   - `rule` → `starterkit/rules/<subfolder>/<name>.md` (subfolder from Step 1 detection)
   - `template` → `starterkit/templates/<name>.md`
2. If the output path already exists, create a backup at `<output-path>.bak` first.
3. Write the approved content.
4. If `--type` is `rule` or `template`, check whether SYNC_MAP.md already has an entry for this file:
   - If missing → propose a new row (do not write it automatically; include in confirmation message).
5. Confirm: `✅ Written to <output-path>`


## Input artifacts

- Legacy source file (required — path provided via `--source`)
- `starterkit/SYNC_MAP.md` — mapping reference
- Current starterkit target file if it exists (for diff-aware updates)

## Output artifacts

- `core` → `starterkit/cores/<name>.md` — cleaned, agent-agnostic, stack-agnostic core prompt
- `rule` → `starterkit/rules/<subfolder>/<name>.md` — cleaned rule; frontmatter preserved; stack-specific tech references removed from common rules
- `template` → `starterkit/templates/<name>.md` — cleaned template; agent/stack refs replaced with starterkit equivalents


## Special case: SDD Handbook (SDD_HANDBOOK.md)

The legacy AGENTS.md (handbook) is not a core, rule, ni template estándar. Se migra manualmente como `/SDD_HANDBOOK.md` y siempre se copia a la raíz del proyecto. No requiere ni soporta migración automática por este comando. Véase SYNC_MAP.md para el mapeo y tratamiento especial.
