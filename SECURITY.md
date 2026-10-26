# Security Policy & Vulnerability Disclosure

TechIndustry takes the security of our users' personal data, educational records, assessment integrity, and authentication credentials with the utmost seriousness.

This document describes supported versions, our vulnerability reporting procedure, and the threat mitigation guidelines governing our software.

---

## 1. Supported Versions

Security patches and bug fixes are applied to the active major and minor releases as outlined below:

| Version | Supported          | Security Maintenance Level |
| ------- | ------------------ | -------------------------- |
| 1.0.x   | Yes | Full Active Support        |
| < 1.0   | No                 | End of Life (Unsupported)  |

---

## 2. Reporting a Vulnerability

If you discover a security vulnerability in TechIndustry, **DO NOT create a public issue on GitHub.**

Please report security issues responsibly through our private security channel:
- **Email:** `security@techindustry.ua` (or open a GitHub Private Security Advisory via the repository's "Security" tab).
- **PGP Encryption:** Available upon request for high-severity reports.

### What to include in your report:
1. **Description & Impact:** High-level summary of the vulnerability and its potential consequence (e.g., Privileged Access Escalation, SQL Injection, CSRF Bypass, Stored XSS in Lesson comments).
2. **Steps to Reproduce:** Minimal reproducible demonstration or Proof-of-Concept (curl command, HTTP payload, or script).
3. **Affected Components:** File paths, route endpoints, database models, or middleware involved.
4. **Remediation Proposal:** Any suggested patches or architectural fixes (optional, but welcome).

---

## 3. Vulnerability Response Timeline & SLA

Our security engineering team adheres to standard industry SLAs:

- **Initial Response & Confirmation:** Within 24 hours of receipt.
- **Triage & Severity Assignment (CVSS v3.1):** Within 48 hours.
- **Remediation & Patch Development:** 
  - *Critical / High (CVSS 7.0 - 10.0):* Within 3 to 7 business days.
  - *Medium / Low (CVSS 0.1 - 6.9):* Within 14 business days or next scheduled release.
- **Public Disclosure:** Coordinated disclosure occurs only after an advisory and hotfix release are deployed.

---

## 4. Key Defensive Invariants in TechIndustry

For a detailed architectural breakdown of our threat mitigation layers, refer to [docs/THREAT_MODEL.md](docs/THREAT_MODEL.md).

- **CSRF Protection:** Double-Submit Cookie scheme via `csurf`. State-changing HTTP methods (`POST`, `PUT`, `DELETE`, `PATCH`) without a validated `_csrf` token are rejected with `403 Forbidden`.
- **Injection Defense:** Sequelize ORM parameterized queries prevent SQL injection. `express-mongo-sanitize` strips operator characters (`$`, `.`) from request parameters.
- **HTTP Hardening:** Helmet enforces strict Content Security Policy (CSP), HTTP Strict Transport Security (HSTS), `X-Content-Type-Options: nosniff`, and `X-Frame-Options: SAMEORIGIN`.
- **Anti-Cheating Mechanics:** Quiz grading, XP awards, and coin allocations are computed strictly server-side within ACID transactions; client requests only convey raw answer selections.
