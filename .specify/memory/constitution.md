<!--
## Sync Impact Report (v1.1.0 → v1.1.1)

**Version change:** 1.1.0 → 1.1.1 (PATCH)
**Bump rationale:** Corrected two path references from `.starterkit/memory/` to `.specify/memory/`; aligned SDD command references from `/starterkit-*` to `/speckit.*` naming convention.

**Modified sections:**
- Governance: `.starterkit/memory/constitution.md` → `.specify/memory/constitution.md`
- Governance: `.starterkit/memory/versions/` → `.specify/memory/versions/`
- SDD & traceability: `/starterkit-spec` → `/speckit.specify`, etc.

**Added sections:** None
**Removed sections:** None

**Templates reviewed:**
- `.specify/templates/plan-template.md` ✅ No updates required
- `.specify/templates/spec-template.md` ✅ No updates required
- `.specify/templates/tasks-template.md` ✅ No updates required

**Deferred TODOs:**
- `RATIFICATION_DATE` for v1.0.0 and v1.1.0 remain as TODO — original adoption date unknown.
-->

# Project Constitution — ContosoDashboard

This document is the **single normative map** for this repository: principles, precedence, stack, and **links** to detailed rules. It does **not** duplicate long-form content — follow the links.

**Precedence (highest first):** mandatory security rules → this constitution → stack-specific `.mdc` rules → style guides.

---

## Project context

**ContosoDashboard** is a fictional project-management dashboard built for **training in Specs-Driven Development (SDD)** using the GitHub Spec Kit. It is not intended for production use.

> **Training scope:** The codebase deliberately simplifies or omits certain production patterns (mock auth, no migrations, permissive CSP). These are documented as known limitations — see [Known limitations](#known-limitations-training-context) below and [`contoso-dashboard.mdc`](../../.cursor/rules/stack-packs/dotnet-blazor/contoso-dashboard.mdc).

---

## Core principles

1. **Specs before code** — Product intent and contracts are agreed in specs and backlog artifacts before implementation; scope is traceable with stable IDs (e.g. `REQ-…`).
2. **No silent scope creep** — The coding agent does not expand scope beyond approved specs and acceptance criteria without explicit confirmation.
3. **Backward-compatible contracts** — Prefer additive API and schema changes; version or flag breaking changes.
4. **Defense in depth** — Security and authorization are enforced at multiple layers (middleware, page attributes, service checks); client checks alone are not sufficient.
5. **Service-layer ownership** — Business logic and resource authorization live in the `Services/` layer, not in Blazor page code blocks.

---

## Specs-Driven Design (SDD) and traceability

- **Rule (always on):** [`specs-driven-design.mdc`](../../.cursor/rules/specs-driven-design.mdc)
- **Backlog shape:** [`backlog_architect.md`](../../.cursor/agents/backlog_architect.md)
- **Templates:** [`../templates/`](../templates/) (e.g. `story_template.md`, `svs_template.md`)
- **Example spec / story:** [`example_feature_spec.md`](../../.cursor/examples/example_feature_spec.md), [`example_story.md`](../../.cursor/examples/example_story.md)
- **SDD commands (full cycle):** [`AGENTS.md`](../../AGENTS.md) — `/speckit.specify` → `/speckit.backlog` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement` → `/speckit.analyze`

**Spec artifact chain (per feature `{slug}`):**

| Artifact | Command that produces it | Content |
| --- | --- | --- |
| `specs/{slug}.spec.md` | `/starterkit-spec` | Problem, goals, constraints, APIs |
| `specs/{slug}.backlog.md` | `/starterkit-backlog` | SVS, stories, `REQ-*` IDs, Gherkin AC, NFRs |
| `specs/{slug}.plan.md` | `/starterkit-refine` *(optional)* | Implementation order, risks, open questions |
| `specs/{slug}.tasks.md` | `/starterkit-tasks` | Numbered tasks (N.M), test subtasks, Jira keys |

---

## Review & quality gates

Post-implementation quality is enforced by the **`@qa-reviewer`** agent, invoked via `/starterkit-review`.

| Gate | Responsibility | Tool |
| --- | --- | --- |
| AC narrative verification | Did we build what the stories said? | [`doc_review_assistant.md`](../../.cursor/agents/doc_review_assistant.md) |
| Technical QA (constitution + completeness + tests) | Did we build it correctly and completely? | [`qa-reviewer.md`](../../.cursor/agents/qa-reviewer.md) via `/starterkit-review` |

**Test coverage target (per story):** 60 % unit / 20 % integration / 20 % E2E.
E2E may be deferred with explicit justification in `tasks.md`; `@qa-reviewer` accepts `deferred` as ⚠️ Partial rather than ❌ Missing.

**Commit traceability standard:**
```
feat(REQ-XXX-NNN): JIRA-KEY Story N – <title>

Tasks: N.1, N.2, N.3
Tests: unit (X), integration (Y)[, e2e (Z) | e2e: deferred – <reason>]

Refs: REQ-XXX-NNN, JIRA-KEY
```
plus `--trailer "Made-with: AI-Assisted by Cursor"`.

---

## Security

- **Global (always on):** [`security.mdc`](../../.cursor/rules/security.mdc)
- **Web / HTTP apps:** [`web-security.mdc`](../../.cursor/rules/web-security.mdc)
- **Stack-specific:** [`contoso-dashboard.mdc`](../../.cursor/rules/stack-packs/dotnet-blazor/contoso-dashboard.mdc) — sections 7 (IDOR), 8 (EF), 12 (training exceptions)

### Security model (implemented)

| Layer | Mechanism |
| --- | --- |
| Transport | HTTPS redirect + HSTS |
| Headers | CSP, `X-Frame-Options: DENY`, `X-Content-Type-Options`, `X-XSS-Protection`, `Referrer-Policy` (custom middleware in `Program.cs`) |
| Authentication | Cookie-based (8h sliding), `CookieAuthenticationDefaults` |
| Identity | Claims: `NameIdentifier` (int), `Name`, `Email`, `Role` |
| Authorization — pages | `@attribute [Authorize]` on all protected Blazor pages |
| Authorization — resources | Service methods receive `requestingUserId`; return `null`/`false` on denial (IDOR pattern) |
| Roles | `Employee`, `TeamLead`, `ProjectManager`, `Administrator` (hierarchical, registered in `Program.cs`) |

---

## Documentation

- **Conventions:** [`documentation.mdc`](../../.cursor/rules/documentation.mdc)

---

## Technology stack

### Runtime

| Item | Value |
| --- | --- |
| Framework | ASP.NET Core 8.0 |
| UI model | Blazor Server (`AddServerSideBlazor`, `MapBlazorHub`) |
| Target framework | `net8.0` |
| Nullable references | `enable` |
| Implicit usings | `enable` |

### Data

| Item | Value |
| --- | --- |
| ORM | Entity Framework Core 8.0.0 |
| Database (training) | SQL Server LocalDB (`EnsureCreated` at startup) |
| Database (production path) | Azure SQL (connection string swap, no code changes required) |
| Schema management | `EnsureCreated` + `HasData` seed for training; **migrations required for production** |

### Frontend

| Item | Value |
| --- | --- |
| CSS framework | Bootstrap 5.3 (CDN) |
| Icons | Bootstrap Icons (CDN) |
| Custom CSS | `wwwroot/css/site.css` — Contoso blue sidebar (`#0078d4 → #005a9e`) |

### Authentication (training)

| Item | Value |
| --- | --- |
| Current | Mock cookie auth — user selected from dropdown, no password |
| Production path | Microsoft Entra ID / Azure AD (OIDC config placeholder in `appsettings.json`) |

### Key NuGet packages

| Package | Version |
| --- | --- |
| `Microsoft.EntityFrameworkCore.SqlServer` | 8.0.0 |
| `Microsoft.EntityFrameworkCore.Tools` | 8.0.0 |
| `Microsoft.Identity.Web` | 2.15.0 (wired but inactive — production auth path) |
| `Microsoft.AspNetCore.Authentication.OpenIdConnect` | 8.0.0 (wired but inactive) |

### Stack-specific rules

[`.cursor/rules/stack-packs/dotnet-blazor/contoso-dashboard.mdc`](../../.cursor/rules/stack-packs/dotnet-blazor/contoso-dashboard.mdc) — layer structure, middleware order, Blazor patterns, auth/userId, IDOR, EF Core, UI/a11y, async conventions.

All available stack packs: [`stack-packs/README.md`](../../.cursor/rules/stack-packs/README.md).

---

## Architecture

### Layer map

```
ContosoDashboard/
├── Data/               EF Core DbContext, fluent config, indexes, seed
├── Models/             Entities + enums (same file per entity)
├── Services/           I*Service interface + implementation (same .cs)
├── Pages/              Blazor Server routed components + Login/Logout Razor Pages
├── Shared/             MainLayout, NavMenu, RedirectToLogin
├── wwwroot/            Static assets (Bootstrap from CDN, custom CSS)
└── Program.cs          DI registration + middleware pipeline
```

### Middleware pipeline order (`Program.cs`)

1. Custom security-headers middleware
2. `UseHttpsRedirection`
3. `UseStaticFiles`
4. `UseRouting`
5. `UseAuthentication`
6. `UseAuthorization`
7. `MapBlazorHub`
8. `MapFallbackToPage("/_Host")`

### Service layer conventions

- All services registered as **Scoped**.
- Interface naming: `I{Domain}Service`.
- Method naming: `{Verb}{Resource}Async` → `Task` / `Task<T>`.
- Resource-access methods receive `requestingUserId` and enforce ownership/membership before returning data.
- Return `null`, `false`, or empty collection on denial — no business-rule exceptions.

### Infrastructure abstraction (cloud migration path)

All infrastructure dependencies use interface abstractions to enable swapping local implementations for cloud services without changing business logic:

- **Database:** LocalDB (training) → Azure SQL (production) — connection string change only.
- **Authentication:** mock cookies (training) → Microsoft Entra ID (production) — middleware swap.
- **File storage:** local filesystem → Azure Blob Storage — `IFileStorageService` implementation swap.

---

## Application pages

| Page | Route | Auth required | Notes |
| --- | --- | --- | --- |
| Login | `/login` | No | Mock user dropdown |
| Dashboard | `/` | Yes | Summary cards, announcements |
| Tasks | `/tasks` | Yes | Filter, sort, status update |
| Projects | `/projects` | Yes | User-scoped list |
| Project Details | `/projects/{id:int}` | Yes (member only) | IDOR check in service |
| Team | `/team` | Yes | Directory view |
| Notifications | `/notifications` | Yes | Read/unread management |
| Profile | `/profile` | Yes | Edit info and preferences |
| Logout | `/logout` | Yes | `SignOutAsync` + redirect |

---

## User roles

| Role | Permissions |
| --- | --- |
| `Employee` | View and update assigned tasks; view member projects; manage own profile |
| `TeamLead` | All Employee permissions + view team member activities |
| `ProjectManager` | All TeamLead permissions + create/manage projects, assign tasks |
| `Administrator` | Full system access |

---

## Known limitations (training context)

These are **intentional** simplifications — not patterns to replicate in production:

| Limitation | Location | Production alternative |
| --- | --- | --- |
| Mock login — no passwords | `Login.cshtml` | Microsoft Entra ID / OIDC |
| `EnsureCreated` instead of migrations | `Program.cs` | `dotnet ef migrations` |
| `AllowedHosts: "*"` | `appsettings.json` | Restrict to known hostnames |
| CSP with `unsafe-inline` / `unsafe-eval` | `Program.cs` middleware | Nonce-based CSP |
| `ValidateAuthenticationStateAsync` always returns `true` | `CustomAuthenticationStateProvider` | Token revalidation against IdP |
| Full user list exposed for login picker | `UserService.GetAllUsersAsync` | Remove or restrict to admin role |
| No rate limiting | — | Middleware or API Gateway |
| No audit logging | — | Structured logging + SIEM |
| No account lockout | — | ASP.NET Core Identity lockout |
| Service gap: `AddTaskCommentAsync` missing membership check | `TaskService.cs` | Add `requestingUserId` check |

---

## Compliance

Link org-specific compliance docs here when they exist (placeholder):

- `docs/compliance/README.md` (create if your process requires it)

---

## Governance

- **This file:** `.specify/memory/constitution.md` is the **current** constitution.
- **History:** When amending materially, archive the previous version under [`.specify/memory/versions/`](versions/README.md) using the naming convention described there.
- **Amendments:** Record version/ratification dates in the table below.

| Version | Ratified | Last amended | Notes |
| --- | --- | --- | --- |
| 1.0.0 | TODO(RATIFICATION_DATE): original adoption date unknown | N/A | Initial kit constitution (generic) |
| 1.1.0 | TODO(RATIFICATION_DATE): original adoption date unknown | 2026-04-20 | Updated with ContosoDashboard stack analysis: tech stack, architecture, security model, known limitations |
| 1.1.1 | 2026-04-24 | 2026-04-24 | Fixed `.starterkit/` → `.specify/` path references; aligned SDD command names to `/speckit.*` convention |
