# TechIndustry — Technology Stack Specification

| Subsystem | Technology | Version | Rationale & Architectural Purpose |
|---|---|---|---|
| **Runtime** | Node.js | v20+ LTS | High-throughput asynchronous event-driven I/O engine. |
| **Framework** | Express.js | 5.2.x | Stable HTTP dispatcher with native promise support in route handlers. |
| **Database** | PostgreSQL | 15+ | Enterprise ACID relational database with robust indexing and JSONB support. |
| **ORM** | Sequelize | 6.37.x | Schema migrations, typed models, managed transactions, and relational association cascades. |
| **View Engine** | Handlebars (`hbs`) | 4.2.x | High-performance Server-Side Rendering (SSR) for fast First Contentful Paint (FCP) and optimal SEO. |
| **AI Inference** | Groq Cloud API | LLaMA 3.3 70B | Ultra-low latency LPU hardware inference for responsive educational conversational tutoring. |
| **AI SDK** | OpenAI SDK | 6.17.x | Universal client library interfacing with Groq's OpenAI-compatible completions endpoint. |
| **PDF Generation** | PDFKit | 0.17.x | Native server-side vector PDF rendering for cryptographically verifiable completion certificates. |
| **Image Processing** | Sharp | 0.34.x | High-speed libvips image resizing, WebP conversion, and avatar optimization. |
| **Security Shield** | Helmet | 8.1.x | Automatic configuration of CSP, HSTS, frame-guard, and X-Content-Type protection. |
| **CSRF Defense** | CSURF | 1.11.x | Cryptographic Double-Submit Cookie scheme protecting against Cross-Site Request Forgery. |
| **Rate Limiter** | Express-Rate-Limit | 8.2.x | Tiered sliding window / token-bucket limiting against DoS and credential stuffing. |
| **Sanitization** | Express-Mongo-Sanitize / HPP | 2.2.x / 0.2.x | Stripping malicious operator symbols and HTTP Parameter Pollution attempts. |
| **Input Validation**| Express-Validator | 7.3.x | Declarative middleware input assertion, typing, and validation. |
| **Markdown Parser** | Marked + XSS | 17.0.x / 1.0.x | Markdown parsing for lessons and course content sanitized against Stored XSS. |
| **Email Dispatch** | Nodemailer | 7.0.x | SMTP client for transactional emails (verification, password resets, achievements). |

---

## Tooling & Development Ecosystem

- **Process Supervisor:** `nodemon` (v3.1.x) for hot reload in local development environments.
- **CLI Migrations:** `sequelize-cli` (v6.6.x) for database schema versioning.
- **Package Manager:** `npm` (Lockfile v3).
