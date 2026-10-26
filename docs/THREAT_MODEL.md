# Threat Model & Security Architecture

| Field | Value |
|---|---|
| Target System | TechIndustry Scalable IT Education Platform & LMS |
| Document Version | 1.0.0 |
| Standard Framework | STRIDE / Microsoft Threat Modeling / OWASP Top 10 |
| Classification | Public Engineering & Security Specification |

---

## 1. Overview & Trust Boundaries

TechIndustry handles sensitive student data, proprietary course curricula, assessment integrity, virtual currency balances, and conversational AI interactions. Every interaction crosses defined network and process boundaries.

```mermaid
flowchart TD
    subgraph UntrustedZone["Untrusted Zone (Public Internet)"]
        User["Learner Browser / Potential Attacker"]
        Bot["Automated Scrapers / Brute-force Bots"]
    end

    subgraph DMZ["Perimeter Defense & Reverse Proxy"]
        Nginx["Nginx Reverse Proxy (TLS 1.3 Termination / HSTS)"]
        RateLimiter["Express-Rate-Limit (Tiered Token-Bucket)"]
        HelmetMW["Helmet Security Headers & CSP"]
        CSURFMW["CSURF (Double-Submit CSRF Cookie)"]
    end

    subgraph AppBoundary["Application Trust Boundary (Node.js Express Monolith)"]
        AuthModule["Auth & Session Guard (bcrypt / JWT)"]
        InputSanitizer["Sanitization Pipeline (HPP / MongoSanitize / XSS)"]
        BusinessServices["Domain Services (ACID Transactions)"]
    end

    subgraph DataStorageBoundary["Data Storage Trust Boundary (Private VPC)"]
        Postgres[(PostgreSQL Relational DB)]
    end

    subgraph ExternalServices["External Integration Boundary (Third-Party APIs)"]
        GroqAPI["Groq Cloud LPU (LLaMA 3.3)"]
        SMTPGateway["SMTP Mail Server"]
    end

    User -->|HTTPS| Nginx
    Bot -->|HTTPS| Nginx
    Nginx --> RateLimiter
    RateLimiter --> HelmetMW
    HelmetMW --> CSURFMW
    CSURFMW --> AuthModule
    AuthModule --> InputSanitizer
    InputSanitizer --> BusinessServices
    BusinessServices -->|Encrypted SQL / Connection Pool| Postgres
    BusinessServices -->|TLS 1.3 + API Key| GroqAPI
    BusinessServices -->|SMTPS (TLS)| SMTPGateway
```

---

## 2. STRIDE Threat Analysis & Defensive Mitigations

### 2.1 Spoofing (Identity Spoofing)
- **Threat:** Attacker attempts credential stuffing or brute force against `/login` and `/register`, or attempts session hijacking.
- **Mitigations:**
  - Strict tiered rate limiting on auth endpoints (max 5 requests per minute per IP for authentication routes).
  - Passwords hashed using `bcrypt` with salt rounds = 12.
  - Session cookies marked with `HttpOnly`, `SameSite=Lax` (or `Strict`), and `Secure` flags in production.
  - Timing-safe comparisons for tokens and verification hashes.

### 2.2 Tampering (Data Modification)
- **Threat:** Manipulating client HTTP requests to grant unauthorized coins, pass quizzes without answering correctly, or forge completion certificates.
- **Mitigations:**
  - **Server-Side Validation:** Quiz answers are never verified on the frontend. The server evaluates user selections against authoritative answers and performs state transitions within database transactions.
  - **Certificate Verification:** Certificates contain a tamper-proof cryptographic reference hash (`UC-XXXX-YYYY-...`) queryable against a public ledger endpoint.
  - **Parameter Tampering:** `hpp` (HTTP Parameter Pollution) strips duplicate parameter injections.

### 2.3 Repudiation
- **Threat:** User claims a shop purchase was unauthorized or denies taking a test.
- **Mitigations:**
  - Append-only `CoinTransactions` table logs every credit/debit with user ID, timestamp, and audit category.
  - `UserProgress` records immutable `completedAt` timestamps for lesson completions.

### 2.4 Information Disclosure (Data Leakage)
- **Threat:** Exposure of database credentials, student personal data, or draft unpublished course materials.
- **Mitigations:**
  - Secrets loaded strictly via environment variables (`dotenv`), with `.env` blacklisted in `.gitignore`.
  - Database queries use strict `attributes: { exclude: ['password'] }` projections.
  - Helmet strips the `X-Powered-By: Express` header to prevent server fingerprinting.
  - Standardized error handlers ensure internal stack traces are suppressed in production.

### 2.5 Denial of Service (DoS)
- **Threat:** Resource starvation through heavy PDF certificate generation requests, expensive AI queries, or volumetric HTTP flooding.
- **Mitigations:**
  - Tiered rate limiters: General endpoints have a 100 req/15min quota, AI endpoints have a dedicated token/request quota.
  - Asynchronous streaming for large assets (sharp image transformations and PDFKit streams).
  - PostgreSQL connection pool caps max connections to prevent DB exhaustion.

### 2.6 Elevation of Privilege
- **Threat:** Regular student attempts to access admin panels (`/admin`) or manage courses.
- **Mitigations:**
  - Role-Based Access Control (RBAC) middleware `isAdmin` checks `req.user.role === 'admin'` before dispatching protected routes.
  - Re-authentication required for critical administrative operations.

---

## 3. AI Safety & Prompt Injection Mitigation

Since TechIndustry embeds an interactive LLM mentor powered by Groq LLaMA 3.3:

1. **Jailbreak Filtering:** `isBlockedRequest()` screens queries against adversarial patterns (`ignore previous instructions`, `developer mode`, `incognito mode`, etc.) before calling the Groq API.
2. **Topic Boundary System Prompt:** The LLM's system prompt strictly confines answers to IT education and the TechIndustry platform.
3. **Token Caps:** Output tokens are strictly capped at `max_tokens: 220` with a low temperature (`0.3`) to prevent verbose hallucination and minimize inference costs.
