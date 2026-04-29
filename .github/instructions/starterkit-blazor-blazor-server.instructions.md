---
description: "Blazor Server conventions — page structure, authorization, user identity, state management"
applyTo:
  - "**/*.razor"
  - "**/*.razor.cs"
---


# Blazor Server Conventions

## Page structure

Every routed Blazor page follows this structure:

```razor
@page "/route"
@attribute [Authorize]
@inject IExampleService ExampleService
@inject AuthenticationStateProvider AuthenticationStateProvider

<PageTitle>Page Title</PageTitle>

@* UI markup *@

@code {
    // fields and lifecycle
}
```

- Every page **must** have `@attribute [Authorize]` and `<PageTitle>`.
- `CascadingAuthenticationState` and `AuthorizeRouteView` are configured once in `App.razor` — do not add them to individual pages.
- This is **Blazor Server**, not WASM. Do not use JavaScript interop patterns or packages that assume client-side rendering.

## Authorization in pages

- Use `[Authorize(Policy = "PolicyName")]` when role-gating is needed — verify the policy is registered in `Program.cs` before adding it.
- Do not rely solely on `[Authorize]` for resource-level access — enforce ownership checks in the service layer (see `dotnet-aspnetcore` rule).

## Obtaining the current user id

```csharp
protected override async Task OnInitializedAsync()
{
    var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
    var userIdStr = authState.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
    if (!int.TryParse(userIdStr, out int userId))
        return; // handle unauthenticated — redirect or return early
    // use userId for service calls
}
```

- Always validate the claim is present and parseable before use.
- Do not cache the user id across requests in a field without re-reading from auth state.

## Async and state management

- Use `async Task` (not `async void`) for all event handlers and lifecycle methods.
- Call `StateHasChanged()` only when Blazor cannot detect the change automatically (e.g. after `Task.Delay` or non-Blazor thread callbacks). Do not add it as a blanket call after every operation.
- `OnInitializedAsync` is the standard entry point for data loading — use `OnParametersSetAsync` only when the component responds to route parameter changes.
- Do not use `EventCallback` for parent-child communication unless introducing a reusable child component.

## UI and accessibility

- Use **Bootstrap 5** classes and a consistent icon library across the project.
- Do not add a second CSS framework or icon library without a design spec.
- Breadcrumbs: `aria-label="breadcrumb"` on the `<nav>`, `aria-current="page"` on the active item.
- Add `<PageTitle>` to every new Blazor page.

## What not to do

- Do not place authorization logic only in the page — enforce ownership checks in the service.
- Do not call `StateHasChanged()` without a specific reason.
- Do not introduce Blazor WebAssembly, gRPC, or OIDC production auth without a feature spec.
- Do not add unbounded queries — apply `Take(N)` on any list not constrained by the data model.
