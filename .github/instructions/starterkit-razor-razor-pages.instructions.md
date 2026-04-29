---
description: "ASP.NET Core Razor Pages conventions — page model structure, routing, auth, and form handling"
applyTo:
  - "**/*.cshtml"
  - "**/*.cshtml.cs"
---


# Razor Pages Conventions

## Page model structure

```csharp
[Authorize]
public class ExampleModel : PageModel
{
    private readonly IExampleService _service;

    public ExampleModel(IExampleService service) => _service = service;

    public ExampleViewModel Data { get; private set; } = default!;

    public async Task<IActionResult> OnGetAsync()
    {
        Data = await _service.GetAsync();
        return Page();
    }

    public async Task<IActionResult> OnPostAsync()
    {
        if (!ModelState.IsValid) return Page();
        await _service.SaveAsync(Input);
        return RedirectToPage("./Index");
    }

    [BindProperty]
    public ExampleInputModel Input { get; set; } = default!;
}
```

- Keep `PageModel` handlers thin; delegate to services.
- Decorate with `[Authorize]` at class level; use `[Authorize(Policy = "...")]` for role-gating — verify the policy is registered in `Program.cs`.
- Use `[BindProperty]` for form inputs; validate via `ModelState.IsValid` before processing.

## Routing

- Razor Pages route by convention from the `Pages/` folder (`/Pages/Admin/Users.cshtml` → `/Admin/Users`).
- Use `@page "{id:int}"` route constraints for strongly typed parameters.
- Prefer `RedirectToPage` over `Redirect` for same-app navigation.

## Form handling & validation

- Use Data Annotations on input models (`[Required]`, `[MaxLength]`, `[EmailAddress]`).
- Display validation errors with `<span asp-validation-for>` and `<div asp-validation-summary>` tag helpers.
- Always re-render the page (return `Page()`) when `ModelState` is invalid — do not redirect on validation failure.

## Security

- Do not disable antiforgery validation (`[IgnoreAntiforgeryToken]`) without documented justification.
- Resource-level authorization belongs in the service layer, not just the `[Authorize]` attribute.
- Never expose internal IDs in URLs without verifying the requesting user has access to that resource.

## What not to do

- Do not put business logic in `PageModel` handlers — delegate to services.
- Do not use `TempData` as a general-purpose store; limit it to one-time success/error messages after redirects.
- Do not mix Razor Pages routing conventions with attribute routing on the same page without understanding the precedence rules.
