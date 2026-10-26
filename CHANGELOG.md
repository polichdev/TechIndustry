# Changelog

All notable changes to the **TechIndustry** platform will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Planned
- Redis cache layer for user progress queries and global leaderboard ranking
- Webhook subscriptions for course completion events
- Multi-tenancy support for enterprise education partners

---

## [1.0.0] - 2026-09-16

### Added
- **Core LMS Architecture:** Modular Layered MVC architecture built on Node.js (Express 5), PostgreSQL, and Sequelize ORM.
- **Server-Side Render Engine:** High-performance Handlebars (`hbs`) templates with partial caching and responsive layout views.
- **AI Mentorship Engine:** Low-latency conversational tutor integration powered by Groq Cloud (`llama-3.3-70b-versatile`) with prompt injection guards and rate throttling.
- **Gamification Economy:**
  - Automated XP progression system with 1,000 algorithmic levels.
  - Dynamic user badges (Level milestones, course streaks, quiz perfection).
  - Virtual coin economy with complete ledger accounting via `CoinTransaction` model.
  - Interactive avatar and customization shop with transactional debit guarantees.
- **Curriculum & Assessments:**
  - Full course catalog with hierarchical `Course` -> `Module` -> `Lesson` data structure.
  - Automated quiz scoring engine with server-side validation.
- **Cryptographic PDF Certificates:**
  - Vector PDF generation using `PDFKit` with high-resolution typography and brand assets.
  - Unique certificate serial hash (`UC-{userId}-{courseId}-{base36}`) with public verification URL.
- **Defense-in-Depth Security Suite:**
  - Tiered token-bucket rate limiting via `express-rate-limit`.
  - Double-Submit CSRF cookie defense via `csurf`.
  - HTTP header hardening via `helmet` and Content Security Policy (CSP).
  - Parameter tampering protection with `hpp` and `express-mongo-sanitize`.
- **Engineering Documentation:**
  - Comprehensive architectural specifications, Threat Model, Data Model, and Architecture Decision Records (ADR).
