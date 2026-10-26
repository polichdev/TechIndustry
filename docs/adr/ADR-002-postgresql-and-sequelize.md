# ADR-002: PostgreSQL and Sequelize ORM for LMS Domain Modeling

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-03 |
| Deciders | TechIndustry Core Team |

---

## Context
Educational platforms require complex, relational data relationships (User $\rightarrow$ Progress $\rightarrow$ Lesson $\rightarrow$ Module $\rightarrow$ Course) and strict consistency guarantees for gamification currencies and certificates.

Two primary database paradigms were evaluated:
1. **NoSQL Document Store (MongoDB):** Highly flexible schemas, but lacks native relational integrity, cascades, and robust ACID transaction guarantees across documents without performance penalties.
2. **Relational Database (PostgreSQL):** Battle-tested ACID compliance, strong foreign key constraints, rich indexing options, and first-class JSONB support for semi-structured data (like badge arrays).

---

## Decision
We selected **PostgreSQL** as the primary datastore, interfaced via **Sequelize ORM**:
- Sequelize provides declarative schema definitions, database versioning/migrations (`sequelize-cli`), and associations (`hasMany`, `belongsTo`, `hasOne`).
- Managed transactions (`db.sequelize.transaction`) are leveraged for all multi-table mutations (e.g. updating progress, award points, and writing to the coin ledger atomically).
- JSONB columns are used strategically for user badge lists to avoid expensive joins for read-heavy profile operations.

---

## Consequences

**Positive:**
- Guaranteed relational integrity with foreign keys and cascade rules.
- Complete data auditability for currency transactions and course progress.
- Clean migration history and reproducible testing fixtures.

**Negative / Accepted:**
- Migrations must be carefully planned and written for zero-downtime production deployments.
- ORM queries must be monitored for N+1 hazards using explicit `include` and `attributes` filtering.
