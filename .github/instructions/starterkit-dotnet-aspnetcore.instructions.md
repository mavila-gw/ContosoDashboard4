---
description: "ASP.NET Core conventions — project setup, layering, middleware pipeline, services, EF Core, and models"
applyTo: "**/*.cs,**/*.csproj,**/appsettings*.json,**/Program.cs"
---

# ASP.NET Core Conventions

## Project setup

- Enable `<Nullable>enable</Nullable>` and `<ImplicitUsings>enable</ImplicitUsings>` in every new project.
- Use nullable reference types throughout; initialize navigation properties with `= null!` where required.
- Do not upgrade NuGet packages without explicit justification — document the reason in the PR.
- Do not activate identity/OIDC packages without a spec for production auth.

## Layer structure

Follow a strict layered layout:

```
Data/ApplicationDbContext.cs   — DbContext, fluent config, optional seed
Models/                        — Entities + enums
Services/I*Service.cs          — Interface + implementation (same file per convention)
Controllers/ or Pages/         — HTTP or UI entry points only; delegate to services
```

- Keep business logic out of controllers/pages; delegate to services.
- Interface and implementation in the **same `.cs` file** is an acceptable convention for small-to-medium services — do not split without team agreement.

## Middleware pipeline order (ASP.NET Core)

Preserve the standard ordering in `Program.cs`:

1. Exception handling / security headers
2. `UseHttpsRedirection`
3. `UseStaticFiles`
4. `UseRouting`
5. `UseAuthentication`
6. `UseAuthorization`
7. Endpoint mapping (`MapControllers`, `MapBlazorHub`, `MapFallbackToPage`, etc.)

- Do not insert middleware between `UseAuthentication` and `UseAuthorization`.
- Security headers belong in dedicated middleware — do not duplicate them per controller or page.

## Services — naming, registration, and async

- Register application services as **Scoped** unless there is a clear reason for Singleton/Transient.
- Interface naming: `I{Domain}Service` (e.g. `IProjectService`).
- Method naming: suffix with `Async`, return `Task` or `Task<T>`.
- Services signal "not found" or "denied" by returning `null`, `false`, or an empty collection — avoid throwing business-rule exceptions.

## Authorization at the service layer (IDOR prevention)

Resource-access methods must receive an explicit `requestingUserId` and perform ownership or membership checks before returning data:

```csharp
var resource = await _context.Resources.FirstOrDefaultAsync(r => r.Id == id);
if (resource == null) return null;
return resource.OwnerId == requestingUserId ? resource : null;
```

- Do not rely solely on `[Authorize]` on the controller/page for resource-level authorization — enforce it in the service.
- Every new service method that returns a user-owned resource must include the same `requestingUserId` check.

## Entity Framework Core

- Use `Include` / `ThenInclude` explicitly — do not rely on lazy loading unless configured.
- Apply `Take(N)` on list queries that could return unbounded results.
- Computed / derived properties: mark with `[NotMapped]` and document which navigation must be loaded.
- Use **Alembic** or **EF Core migrations** for schema changes; avoid `EnsureCreated` outside development/test environments.
- Define indexes in `OnModelCreating` for fields used in `Where` / `OrderBy` clauses.

## Models

- Primary key naming: `{Entity}Id` (e.g. `ProjectId`, `TaskId`).
- Decorate PKs with `[Key]`; required strings with `[Required]` and `[MaxLength(N)]`; email fields with `[EmailAddress]`.
- Define enums in the **same `.cs` file** as the model that owns them.
- Do not hardcode secrets, connection strings, or credentials — use `appsettings.json` or environment variables / secret stores.
