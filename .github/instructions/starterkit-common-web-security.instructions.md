---
description: "Security practices for web frontends and backend APIs (stack-agnostic)"
applyTo:
  - "**/*.ts"
  - "**/*.js"
  - "**/*.py"
  - "**/*.cs"
  - "**/*.html"
  - "**/*.razor"
---


# Web Security

## Authentication & authorization

- **Frontend:** Use route guards / auth middleware to protect pages; use interceptors or middleware to attach tokens; do not store long-lived secrets in browser storage if the threat model forbids it — follow team standard.
- **Backend:** Validate tokens (JWT, session) on every protected route; enforce RBAC/ABAC in the service layer, not only in route decorators.

## Input validation

- Validate and parse all request bodies and query parameters with a schema or validation library (e.g. Pydantic, Zod, FluentValidation); reject oversize payloads at the gateway or framework level.
- **Frontend:** Sanitize untrusted HTML before rendering; validate file uploads (type, size) server-side.

## Sensitive data

- Never log passwords, tokens, API keys, or unnecessary PII.
- Mask or redact sensitive fields in API responses and error messages.
- Do not embed secrets in source code, environment files committed to VCS, or client-side bundles.

## CSRF & transport

- Use CSRF tokens or same-site cookie attributes for state-mutating operations when cookies are the auth mechanism.
- Enforce HTTPS for all endpoints; reject plain-HTTP connections in production.

## Content security

- Set appropriate `Content-Security-Policy`, `X-Frame-Options`, and `X-Content-Type-Options` headers.
- Avoid `eval`, `innerHTML` with untrusted content, or equivalent injection sinks in frontend code.
