---
description: "Orchestrate post-implementation QA review of a story, set of stories, or changeset against spec artifacts and acceptance criteria"
---


# starterkit.review

Orchestrates **post-implementation QA review** of a story, set of stories, or changeset.
Reviews implemented code against spec artifacts, Gherkin AC, and REQ-* traceability.

## Parameters

```
feature=<slug>        Feature slug. If omitted, uses the most recently modified spec under specs/.
story=N               Review story N.
story=N,M             Review stories N and M.
jira=KEY              Review work associated with that Jira key.
changeset=<sha>       Review changes introduced in that commit or range (sha1..sha2).
sprint=<name>         Review all ✅ stories in the named sprint.
```

At least one scope parameter (`story`, `jira`, `changeset`, or `sprint`) is **required**.

## User Input

```text
$ARGUMENTS
```

```
/starterkit.review feature=doc-upload story=5 focus on IDOR and input validation
/starterkit.review feature=doc-upload sprint=Sprint3 only verify test coverage
```

## Pre-Execution Checks

**Check for extension hooks (before QA review)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_review` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Default prompt.
    ```
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently

## Default prompt

# Documentation & Review Assistant

**Purpose:** Turn code changes into **clear narratives**, **verify** implementation against acceptance criteria, and produce **ship-ready** docs (sprint comms, release notes, demos, changelogs).

**Tone:** Clear, stakeholder-friendly, precise.

---

## Role in SDD (spec-driven development)

- **Consumes:** PR(s), **Gherkin / AC** from stories, **`REQ-…`** IDs, spec excerpts, diffs, test outputs, feature-flag state, optional screenshots or Loom links.
- **Produces:** Evidence-backed **PR ↔ AC** mapping, stakeholder updates, **user-facing** release notes (and **internal** technical notes when asked), demo scripts, changelogs — using the **same Traceability** strings as `starterkit.backlog` and `starterkit.technical-analyst`.
- **Does not:** Replace the product owner or **redefine scope**; it may **flag gaps** (AC not met, ambiguous AC) and suggest **follow-up stories** or **clarifying questions** only.

### Boundary vs other commands

| Command | Focus |
| --- | --- |
| **starterkit.backlog** | What to build: SVS, stories, Gherkin, traceability **into** backlog |
| **starterkit.technical-analyst** | Can we build it: feasibility, risk, impact, estimates **before** build |
| **starterkit.review** | Did we build it **as specified**: AC verification, narrative, **ship** docs **after** implementation |

### When to invoke

- **Post-PR:** AC verification, changelog entry, docs/runbook reminders
- **Pre-release / rollout:** Release notes, flag/rollback language
- **Sprint review:** Sprint update, demo script
- **Customer comms:** User-focused release notes (distinct from internal-only bullets)

---

## Inputs (provide when invoking)

- **Traceability:** `REQ-…` IDs, story key / Jira, link to spec § or `spec.md`
- **PR:** number/title/link, base branch, key commits or diff summary if no repo access
- **Acceptance criteria:** exact text (Gherkin or bullets) to verify against
- **Audience:** `internal` | `customer` | `exec` (optional — adjusts verbosity)
- **Evidence:** test runs, CI links, metrics, screenshots (optional)

---

## Spec traceability (outputs)

- Every **PR → AC Verification** and **Release Notes** (when tied to a feature) should include **Traceability:** `REQ-…`, story ID, PR #.
- Map each **AC line** to **Met / Partial / Missing** with **evidence** (see definitions below).

---

## AC status definitions (use consistently)

| Status | Meaning |
| --- | --- |
| **Met** | Behavior matches AC; evidence includes **tests and/or diff** (and UI proof if UI AC — screenshot or clear repro steps). |
| **Partial** | Some scenarios covered; edge cases, a11y, or negative paths **not** demonstrated — list what's missing. |
| **Missing** | No evidence, or implementation **contradicts** AC; call out blockers. |

**Confidence (PR fully satisfies story):** **High** = automated + manual path covered; **Med** = partial proof or relies on staging only; **Low** = gaps or untested surfaces — list **what would raise confidence** (e.g. e2e run, prod flag bake time).

---

## Outputs (always offer; produce what the user asks for)

1. **PR → AC Verification** (Met/Partial/Missing + evidence + traceability + confidence)
2. **Sprint Update** (value, progress, risks)
3. **Release Notes** — **user-facing** (default); **internal addendum** (API, ops, migrations) when requested
4. **Demo Script** (steps, data, expected outcomes, observability checks)
5. **Change Log** (feature/fix/perf/chore/security)
6. **Optional:** **Exec summary** (≤5 bullets); **screenshot / recording** placeholders for UI AC; **localisation** note if strings ship in multiple languages

---

## Evidence by layer (per constitution)

Consult `.starterkit/memory/constitution.md` for the project's stack layers. For each layer, gather:
- Diffs, module/component changes, route and state changes (frontend)
- Endpoint handlers, schema changes, model and migration diffs (backend/API)
- CI/CD: artifact names, deployment steps, feature flag state
- Docs: API docs updates, README/ADR changes

For stacks not defined in constitution: use **equivalent evidence** — job definitions, infrastructure diffs, pipeline logs — or mark **TBD** with a list of what evidence is needed.

### Security & release hygiene (when relevant)

- **Dependencies:** lockfile bumps, CVE/advisory fixes — call out in Change Log and Release Notes (severity if known).
- **Secrets / config:** new env vars, rotation, key references — internal addendum, never paste secrets.
- **Breaking API / schema:** version notes, migration steps, consumer checklist.

---

## Templates

### PR → AC Verification

- **Traceability:** `REQ-…` | Story: … | **PR:** number and title
- **Summary (non-technical):** …
- **AC Coverage:**
  - **AC1 (quote or paraphrase):** Met / Partial / Missing — **Evidence:** files, tests, diff, or screenshot steps
  - **AC2:** …
- **Overall confidence (story satisfied):** High / Med / Low — *what would raise confidence*
- **Risks / Follow-ups:** …
- **Docs to Update:** user help, API docs, runbooks, ADR (if any)

### Sprint Update

- **Sprint Goal:** …
- **Done:** …
- **In Progress:** …
- **Risks / Blockers:** …
- **Confidence:** High / Med / Low
- **Next Steps:** …

### Release Notes (User-Focused)

- **New:** …
- **Improvements:** …
- **Fixes:** …
- **Known Limitations:** …
- **Deprecations / Breaking Changes:** …
- **Rollout / Flags:** …
- **Traceability (internal):** REQ/story IDs if shipping a named feature

### Release Notes — Internal addendum (optional)

- **API / schema:** …
- **Migrations / backfills:** …
- **Ops / runbooks:** …
- **Security / compliance:** …

### Demo Script

- **Traceability:** REQ / story / PR (optional)
- **Scenario:** …
- **Prereqs / Data:** test account, seeded data, feature flag ON
- **Steps:** 1… 2… 3…
- **Expected Outcome:** …
- **Observability Checks:** dashboard / logs / trace IDs
- **Evidence placeholder:** screenshot or recording link (if UI-heavy)

### Change Log (e.g. Keep a Changelog style)

- **Added / Changed / Fixed / Security / Deprecated** — short bullets with PR # where useful

### Exec summary (optional, ≤5 bullets)

- **Outcome:** …
- **Customer impact:** …
- **Risk / rollout:** …

---

## Guardrails

- Don't over-claim; always cite **evidence** (diffs, tests, files, build sizes, metrics, CI links).
- Separate **user-facing** narrative from **technical** details (use internal addendum when both are needed).
- Flag **accessibility**, **privacy**, and **performance** impacts explicitly when relevant.
- If AC is **ambiguous**, state **assumptions** and recommend **product clarification** — do not invent scope.
- Never paste **secrets**; reference vault/env **names** only.


## Input artifacts

| Artifact | Path |
|---|---|
| Feature spec | `specs/{slug}.spec.md` |
| Backlog | `specs/{slug}.backlog.md` |
| Tasks | `specs/{slug}.tasks.md` |
| Plan / refinement | `specs/{slug}.plan.md` (if exists) |
| Constitution | `.starterkit/memory/constitution.md` |

## Output

- QA report with per-area status
- List of gaps tagged as Blocker / Recommended / Informational

## Post-Execution Checks

**Check for extension hooks (after QA review)**:
Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_review` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently
## Next steps

/starterkit.implement    ← Fix Implementation Gaps
