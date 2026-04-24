---
description: "Specs-Driven Design (SDD) workflow for coding agent — specs before code, traceability end-to-end"
---

# Specs-Driven Design (SDD)

This project uses **SDD**: product intent and contracts are captured in **specs** and **backlog artifacts** before implementation. The coding agent implements against **approved scope**, not ad-hoc assumptions.

## Artifact chain (typical)

1. **Feature spec** — Problem, goals/non-goals, constraints, conceptual APIs (`specs/{slug}/{slug}.spec.md`).
2. **Requirement IDs** — Stable strings such as `REQ-{FEATURE}-{NNN}` used in stories, plan, tasks, and PRs.
3. **Stories & AC** — Gherkin, NFRs, traceability (`specs/{slug}/{slug}.backlog.md`). Templates in `.specify/presets/starterkit/templates/`.
4. **Plan & Tasks** — Implementation steps derived from spec; link `REQ-*` IDs (`specs/{slug}/{slug}.plan.md`, `specs/{slug}/{slug}.tasks.md`).
5. **Code + tests** — Map changes to the same IDs in commit messages or PR descriptions when the team requires it.

## Rules for the coding agent

- **Do not expand scope** beyond the spec's goals and the story's AC without explicit user confirmation.
- **Prefer additive, backward-compatible** API and schema changes; version or flag breaking changes.
- **API contracts** and **implementation models** stay in sync; update spec snippets when contracts change.
- If the spec is **missing or ambiguous**, list **assumptions** and **questions** before large implementations — do not invent product requirements.

## Traceability

- Reference **`REQ-*`** IDs when editing files tied to a feature.
- Keep `specs/{slug}.spec.md` as the single source of truth for feature scope.
- Do not invent scope outside the spec's goals; if something is missing, list it as an Open Question.
- If the spec has Non-Goals, no story or task may cover them.

## Where to look

- **Policy and precedence:** `.specify/memory/constitution.md`
- `sdd-workflow` rule — Stepped workflow with review gates (always applied)
- `output-config` rule — Output directory configuration (always applied)
- `.specify/presets/starterkit/templates/story_template.md` — Shape for each user story
- `.specify/presets/starterkit/templates/svs_template.md` — Sprint-sized Slice block
