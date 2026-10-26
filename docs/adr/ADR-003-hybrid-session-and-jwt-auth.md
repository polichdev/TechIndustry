# ADR-003: Hybrid Session Cookies and JWT Authentication Architecture

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-05 |
| Deciders | TechIndustry Core Team |

---

## Context
TechIndustry delivers both a Server-Side Rendered (SSR) web portal and JSON REST endpoints consumed by client-side widgets (such as the interactive AI chat, lesson completion triggers, and mobile views).

Using pure JWT stored in browser `localStorage` exposes tokens to Cross-Site Scripting (XSS). Conversely, pure stateful server-side sessions can introduce friction for programmatic API consumers and mobile integrations.

---

## Decision
We implemented a **Hybrid Authentication Model**:
1. **SSR Web Portal:** Uses encrypted, signed `HttpOnly`, `SameSite=Lax`, `Secure` session cookies managed by `express-session`. Browser JavaScript cannot access the session identifier, eliminating token theft via XSS.
2. **API & OAuth Integrations:** Employs stateless **JSON Web Tokens (JWT)** signed using HMAC-SHA256 (`jsonwebtoken`) with short expiration windows for API endpoints and OAuth provider callbacks.

---

## Consequences

**Positive:**
- Top-tier security posture for web users (immune to `localStorage` XSS attacks).
- Seamless API interoperability for mobile clients and headless integrations.
- Backward-compatible authentication checks supported across all route pipelines.

**Negative / Accepted:**
- Two authentication middleware paths must be maintained (`isAuthenticated` for sessions and `verifyJwt` for bearer tokens).
