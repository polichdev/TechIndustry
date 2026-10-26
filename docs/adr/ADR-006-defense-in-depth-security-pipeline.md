# ADR-006: Multi-Layered Defense-in-Depth Security Pipeline

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-14 |
| Deciders | TechIndustry Core Team |

---

## Context
As a public educational web platform storing user emails, password hashes, and learning credentials, TechIndustry is an inherent target for automated bot traffic, brute force attacks, credential stuffing, and injection attempts.

Relying on a single perimeter defense (e.g., just an Nginx firewall or basic ORM escaping) leaves the platform vulnerable to defense-in-depth failures.

---

## Decision
We implemented a strict **Multi-Layered Security Pipeline** embedded across Express middlewares:

1. **HTTP Security Headers (`helmet`):** Automatically injects strict CSP (Content Security Policy), HSTS (`Strict-Transport-Security`), `X-Content-Type-Options: nosniff`, and disables DNS prefetching.
2. **CSRF Mitigation (`csurf`):** Implements Double-Submit Cookie protection. All non-idempotent HTTP methods (`POST`, `PUT`, `DELETE`, `PATCH`) must supply the cryptographically validated token.
3. **Tiered Rate Limiting (`express-rate-limit`):**
   - General API: 100 requests per 15-minute window per IP.
   - Auth endpoints (`/login`, `/register`, `/forgot-password`): 5 requests per minute per IP to defeat credential brute forcing.
   - AI Chat endpoint: Strict limits to protect Groq API quota.
4. **Sanitization Shield:**
   - `express-mongo-sanitize`: Eliminates prohibited operator characters (`$`, `.`) from request inputs.
   - `hpp`: Protects against HTTP Parameter Pollution.
   - `xss`: Filters rich Markdown lesson content before rendering.

---

## Consequences

**Positive:**
- Comprehensive mitigation of OWASP Top 10 vulnerabilities (XSS, CSRF, Injection, Broken Auth).
- Resilience against denial-of-service and bot scrapers.
- Compliance with modern web application security audit standards.

**Negative / Accepted:**
- CSRF token must be passed in AJAX payloads and form bodies, requiring consistent templating helper injection in all Handlebars views.
