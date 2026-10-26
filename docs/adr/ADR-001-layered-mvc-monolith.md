# ADR-001: Modular Layered MVC Monolith Architecture

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-01 |
| Deciders | TechIndustry Core Team |

---

## Context
When designing the TechIndustry education and e-learning platform, three top-level architecture paradigms were evaluated:
1. **Microservices Architecture:** Decomposing authentication, course management, gamification, and AI into separate services communicating over gRPC/REST.
2. **Single Page Application (SPA) + Headless API:** React/Vue frontend coupled with a pure JSON REST backend.
3. **Modular Layered MVC Monolith:** Node.js Express 5 with Server-Side Rendering (SSR) via Handlebars (`hbs`) and clean service-layer boundaries.

Microservices introduce excessive network latency, complex distributed transaction orchestration (Saga patterns), and steep deployment overhead that is unwarranted for this stage. A client-rendered SPA presents challenges for course search engine optimization (SEO) and requires double the state management complexity.

---

## Decision
We adopted a **Modular Layered MVC Monolith** architecture:
- **Express 5** acts as the high-throughput HTTP controller and routing engine.
- **Handlebars (`hbs`)** provides server-side rendering for optimal SEO, rapid first contentful paint (FCP), and minimal client-side JavaScript overhead.
- Business logic is strictly isolated in a decoupled `src/services/` layer, allowing future extraction into standalone microservices or workers without modifying core domain algorithms.

---

## Consequences

**Positive:**
- Sub-50ms SSR page response times; excellent SEO visibility for public courses.
- Single deployment artifact; vastly simplified CI/CD pipeline and monitoring.
- Atomic local database transactions without distributed commit overhead.

**Negative / Accepted:**
- Scaling requires replicating the entire application monolith rather than individual high-load services (mitigated by PM2 cluster mode and stateless sessions).
