# SDD Handbook (Universal Entry Point)

This handbook is always loaded by AI coding assistants (Copilot, Cursor, etc.) and serves as the single authoritative map and workflow guide for Spec-Driven Development (SDD) projects using the starterkit.

---

## SDD Workflow at a Glance

Artifacts are generated one step at a time, with review gates after each step:

| Step | What you get | What to do |
| --- | --- | --- |
| **1. Feature spec** | `spec.md` — Problem, goals/non-goals, constraints, APIs, observability. Example: `.starterkit/examples/example_feature_spec.md`. | **Review and confirm** before proceeding. |
| **2. Backlog** | `backlog.md` + `svs.md` + `stories.md` — REQ IDs, sprint-sized slices, Gherkin AC, NFRs. Template: `.starterkit/templates/story_template.md`. | **Review and confirm** before proceeding. |
| **3. Plan** | `plan.md` — Task breakdown, dependencies, deployment order, risk register. | **Review and confirm** before proceeding. |
| **4. Publish** *(optional)* | Initiative doc for Confluence and/or Jira YAML — only when you ask. | **Review** before publishing. |

All output goes to `output/{project-name}/` in your workspace. See `starterkit/rules/common/output-config.md`.

**Skip-ahead:** If you want multiple steps at once, say so explicitly (e.g. "generate everything" or "skip to plan"). Otherwise, the assistant will stop after each step.

**Brownfield note:** In existing repos, run `/starterkit.council` before Step 1, then attach the real files you expect to touch from Step 1 onward.

---

## Standalone Command Usage

You can invoke any starterkit command independently at any step without advancing the workflow. This is useful for feasibility checks, risk analysis, or challenging assumptions before confirming a step.

| Command | Use case |
| --- | --- |
| `starterkit.technical-analyst` | Assess feasibility, complexity, and risks — at any step |
| `starterkit.devils-advocate` | Challenge assumptions, surface edge cases — at any step |
| `starterkit.review` | Verify PRs against AC, generate release notes — after implementation |
| `starterkit.backlog` | Generate stories directly if you already have a confirmed spec |

---

## Where Things Live

| What | Location |
| --- | --- |
| **Rules** (always-on + file patterns) | `.starterkit/rules/` |
| **Commands** (long-form roles: backlog, analyst, docs, Confluence, etc.) | `.starterkit/commands/` |
| **Templates** (story, SVS, etc.) | `.starterkit/templates/` |
| **Examples** | `.starterkit/examples/` |
| **Generated output** | `output/{project-name}/` (see `output-config.md`) |
| **Practical usage guide** | `.starterkit/USAGE_GUIDE.md` |

---

## Rules (Coding Agent + Humans)

- **Always applied:**
  - `starterkit/rules/common/sdd-principles.md` — SDD guardrails and traceability.
  - `starterkit/rules/common/sdd-workflow.md` — Stepped workflow with review gates.
  - `starterkit/rules/common/output-config.md` — Output directory configuration.
  - `starterkit/rules/common/security.md` — Mandatory OWASP-based security guardrails.
- **Stack-specific:**
  - Angular: `starterkit/rules/angular/angular-patterns.md`
  - Python: `starterkit/rules/python/python-api.md`, etc.
  - Dotnet, Blazor, Razor: see respective subfolders.
- **Cross-cutting:**
  - `starterkit/rules/common/web-security.md`, `starterkit/rules/common/documentation.md`

Each rule file lists globs (when not always-on). Adjust globs if your folders differ.

---

## Main Commands (When to Use Which)

| Command | Role | Typical step | Standalone? |
| --- | --- | --- | --- |
| `starterkit.backlog` | SVS, user stories, Gherkin, tasks — backlog shape for SDD. | Step 2 | Yes |
| `starterkit.technical-analyst` | Feasibility, risk, impact, estimates — before major build. | Any | Yes |
| `starterkit.checklist` | After AC stable: formal test design mapped to each AC; QA execution view. | After backlog | Yes |
| `starterkit.testing` | Devs while coding: unit tests, use cases beyond AC, mocks, DoD(dev test). | During implementation | Yes |
| `starterkit.confluence-automation` | Initiative doc → Confluence (with your page id / MCP). | Step 4 | Yes |
| `starterkit.review` | PR ↔ AC verification, release notes, demos — after implementation. | Post-Step 4 | Yes |
| `starterkit.devils-advocate` | Challenge plans and specs — risks, edge cases, alternatives. | Any | Yes |

---

## Skills (Optional Automation)

- **Jira hierarchy** — `.starterkit/cores/jira-automation.md` — Epic → Story → Task order and YAML-friendly structure; align summaries with `REQ-…` when using SDD.
- **Code cleanup** — `.starterkit/cores/clean-code.md` — Naming, noise, style for any stack.

---

## Conventions Worth Repeating

- **Traceability:** Put `REQ-…` (and story / Jira key when you use it) in specs, stories, and PR descriptions as your process requires.
- **Contracts:** Keep API behavior, models, and documentation aligned when APIs change.
- **Scope:** Assistants must not invent product scope; resolve ambiguity with the spec owner or explicit user direction.
- **Brownfield:** Explore first with `/starterkit.council`, then attach real files from Step 1 onward and constrain implementation prompts to named files where practical.
- **Output:** All generated artifacts go to `output/{project-name}/` — never to arbitrary locations outside the workspace.

---

**If you add new rules or commands, link them from this file so the entry point stays accurate.**

---

*This handbook is always copied to the project root and loaded by all supported agents (Copilot, Cursor, etc.).*
