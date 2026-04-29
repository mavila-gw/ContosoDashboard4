---
applyTo: "**"
---


# SDD Workflow Orchestration (Mandatory)

This rule enforces a **stepped, gated workflow** for generating SDD artifacts. The agent MUST produce only one step's output per response and MUST NOT proceed to the next step without explicit user confirmation.

## Steps and Gates

### Step 1 — Feature Spec

**Trigger:** User describes a problem, feature, initiative, or objective.

**Command:** `/speckit.specify`

**Output:** `specs/{slug}.spec.md` ONLY — containing:
- Problem statement
- Goals / non-goals
- Constraints
- User acceptance criteria
- Conceptual APIs (if applicable)
- Observability considerations

**Gate:** After generating the feature spec, STOP and present it for review:
> "Feature spec is ready for review. Please review and confirm before I proceed to the backlog stories. If changes are needed, let me know."

**Hard rule:** Do NOT generate requirement IDs, stories, SVS, plans, or tasks in this step. Only the feature spec.


### Step 2 — Clarify (optional)

**Trigger:** User identifies open questions or underspecified areas in the spec.

**Command:** `/speckit.clarify`

**Output:** Updated `specs/{slug}.spec.md` with open questions resolved.

**Gate:** Proceed to Step 3 only when the spec is considered complete.


### Step 3 — Backlog (SVS + Stories + REQ-* IDs)

**Trigger:** User explicitly confirms the feature spec.

**Command:** `/speckit.backlog`

**Output:** `specs/{slug}/{slug}.backlog.md` — SVS, stories with stable `REQ-{FEATURE}-{NNN}` IDs, Gherkin AC, NFRs, task breakdown.

Use templates in `.specify/presets/starterkit/templates/`: `svs_template.md`, `story_template.md`, `acceptance_criteria_gherkin.md`.

**Gate:** After generating backlog artifacts, STOP and present for review:
> "Backlog artifacts (SVS and stories) are ready for review. Please review and confirm before I proceed to the implementation plan."

**Hard rule:** Do NOT generate plan or tasks in this step.


### Step 4 — Plan / Tasks

**Trigger:** User explicitly confirms the backlog artifacts.

**Commands:** `/speckit.plan` then `/speckit.tasks`

**Output:**
- `specs/{slug}/{slug}.plan.md` — Implementation plan with task breakdown and risk register
- `specs/{slug}/{slug}.tasks.md` — Actionable task list derived from the plan

**Gate:** After generating the plan, STOP and present for review:
> "Implementation plan is ready for review. Please review and confirm before I proceed to publishing artifacts."


### Step 5 — Publishing Artifacts (optional)

**Trigger:** User explicitly requests Confluence or Jira output.

**Commands:** `/starterkit.jira-automation` (Jira hierarchy); Confluence via initiative template.

**Output (one or both, as requested):**
- `specs/{slug}/{slug}.initiative.md` — Confluence initiative document (use `templates/initiative_confluence_skeleton.md`)
- Jira Epic/Story/Task hierarchy — use `starterkit.jira-automation` for structure and YAML schema

**Gate:** Present for review before any MCP publish action.


## Rules (always enforced)

1. **One step per response.** Never combine Step 1 + Step 3 output in a single response, even if the user's prompt contains enough detail for multiple steps.

2. **Never assume approval.** Phrases like "here's everything" or "I'll also generate the stories" are violations. Always wait for explicit confirmation.

3. **Confirmation keywords.** Treat the following as explicit step approval: "approved", "confirmed", "looks good", "proceed", "go ahead", "next step", "continue", "yes", "LGTM". If ambiguous, ask: "Should I proceed to Step N?"

4. **Skip-ahead escape hatch.** If the user explicitly requests multiple steps at once (e.g. "generate spec and stories together", "give me everything", "skip to plan"), honor that request but note which steps were combined. This is the ONLY exception to the one-step rule.

5. **Step detection.** At the start of each response, determine which step the user is in:
   - No existing artifacts in `specs/` → Step 1
   - Feature spec exists but not confirmed → Present spec for review
   - Feature spec confirmed, no backlog → Step 3
   - Backlog confirmed, no plan → Step 4

**Gate:** After generating the plan, STOP and present for review before generating tasks.


### Step 5 — Implementation

**Trigger:** User explicitly confirms the tasks.

**Command:** `/speckit.implement`


## Rules (always enforced)

1. **One step per response.** Never combine Step 1 + Step 3 output in a single response, even if the user's prompt contains enough detail for multiple steps.

2. **Never assume approval.** Always wait for explicit confirmation before advancing.

3. **Confirmation keywords.** Treat the following as explicit step approval: "approved", "confirmed", "looks good", "proceed", "go ahead", "next step", "continue", "yes", "LGTM". If ambiguous, ask: "Should I proceed to Step N?"

4. **Skip-ahead escape hatch.** If the user explicitly requests multiple steps at once, honor that request but note which steps were combined. This is the ONLY exception to the one-step rule.

5. **Step detection.** At the start of each response, determine which step the user is in based on which spec-kit artifacts exist under `specs/`.

6. **Traceability.** The `REQ-*` IDs generated in Step 3 are the ones that flow into tasks, commits, and code comments.


## Step Summary Table

| Step | Command | Input Required | Output |
|------|---------|---------------|--------|
| 1. Feature Spec | `/speckit.specify` | Problem statement | `specs/{slug}/{slug}.spec.md` |
| 2. Clarify | `/speckit.clarify` | Open questions | Updated spec |
| 3. Backlog | `/speckit.backlog` | Approved spec | `specs/{slug}/{slug}.backlog.md` |
| 4. Plan + Tasks | `/speckit.plan`, `/speckit.tasks` | Approved backlog | `specs/{slug}/{slug}.plan.md`, `specs/{slug}/{slug}.tasks.md` |
| 5. Implement | `/speckit.implement` | Approved tasks | Code |


## Anti-Patterns

- Generating stories in the same response as the feature spec
- Generating a plan before stories are reviewed
- Assuming the user wants all steps because they gave a detailed prompt
- Inventing scope outside the approved spec
- Skipping the gate confirmation between steps
