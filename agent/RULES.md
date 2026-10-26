# TechIndustry Development Rules & Invariants

This document establishes the **mandatory rules**, coding standards, and architectural invariants that must be strictly followed when writing and modifying code in **TechIndustry**.

---

## 1. Iron Architectural Invariants

### 🔴 Rule 1.1: Strict Transactional Integrity for State Mutations
- **Prohibition:** Under no circumstances shall multi-table mutations (e.g. updating user XP, level upgrades, deducting coins for shop purchases, creating certificate records) be executed across separate, unmanaged queries.
- **Rationale:** If any query in a gamification or payment sequence fails (e.g. network timeout or constraint violation), the database would enter an inconsistent state where coins are deducted without item delivery, or XP is granted without level re-indexing.
- **Requirement:**
  All business mutations modifying multiple entities must be wrapped in a managed Sequelize transaction:
  ```javascript
  await db.sequelize.transaction(async (t) => {
      await user.update({ xp: newXp }, { transaction: t });
      await db.CoinTransaction.create({ ... }, { transaction: t });
  });
  ```

### 🔴 Rule 1.2: No Raw SQL String Interpolation (Zero SQL Injection)
- **Prohibition:** Never interpolate or concatenate user input or runtime variables into SQL strings (e.g. `` db.sequelize.query(`SELECT * FROM users WHERE id = ${req.body.id}`) `` is strictly banned).
- **Rationale:** SQL injection remains one of the top OWASP vulnerabilities. Concatenation invalidates query caching and opens catastrophic data breaches.
- **Requirement:**
  Always use Sequelize Model methods (`findByPk`, `findOne`, `create`, `update`) or pass bind/replacement parameters:
  ```javascript
  await db.sequelize.query('SELECT * FROM "Users" WHERE id = :userId', {
      replacements: { userId },
      type: QueryTypes.SELECT
  });
  ```

### 🔴 Rule 1.3: Mandatory Input Validation via Express-Validator
- **Prohibition:** Controllers must never process raw `req.body`, `req.params`, or `req.query` without schema validation.
- **Rationale:** Processing unvalidated input leads to type coercion bugs, unexpected `null` dereferencing, and parameter pollution.
- **Requirement:**
  Declare validation rules in routing middleware and reject malformed requests immediately:
  ```javascript
  const { validationResult } = require('express-validator');
  // In controller:
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
  }
  ```

### 🔴 Rule 1.4: Separation of Concerns (Slim Controllers, Rich Services)
- **Prohibition:** Do not place database queries, third-party API calls, or business calculations inside Express route handlers/controllers.
- **Rationale:** Controllers are purely HTTP dispatchers. Mixing business logic with HTTP mechanics prevents unit testability, code reuse, and clean auditing.
- **Requirement:**
  - `controllers/`: Parse HTTP request $\rightarrow$ delegate to `services/` $\rightarrow$ format HTTP response (or SSR render).
  - `services/`: Encapsulate domain logic, transactions, third-party integrations, and database operations.
  - `models/`: Define schema definitions, validations, hooks, and relational associations.

---

## 2. Security Standards

1. **CSRF Enforcement:**
   - All state-changing HTTP requests (`POST`, `PUT`, `DELETE`, `PATCH`) must validate the `_csrf` token issued by `csurf`.
   - Never disable CSRF protection globally. If an external API requires webhook access, apply specific route-level exclusions with HMAC signature verification.

2. **XSS & Template Escaping:**
   - In Handlebars SSR views, default to double braces `{{ variable }}` for automatic HTML entity escaping.
   - Triple braces `{{{ htmlContent }}}` are restricted exclusively to parsed Markdown content that has already passed through `xss(marked.parse(content))`.

3. **Rate Limiting:**
   - Sensitive endpoints (auth `/login`, `/register`, `/forgot-password`, and `/api/ai/chat`) must be protected with dedicated tiered rate limiters to mitigate brute force and API cost exhaustion.

---

## 3. Database & Sequelize Standards

1. **Naming Conventions:**
   - Table names: PascalCase plural (e.g., `Users`, `Courses`, `UserProgresses`).
   - Column names: camelCase (e.g., `createdAt`, `userId`, `isCompleted`).
   - Foreign keys: `<singularEntity>Id` (e.g., `userId`, `courseId`).
2. **Eager Loading vs. N+1 Queries:**
   - Avoid querying inside loops. Use Sequelize `include` options with appropriate `attributes` filtering to prevent loading unused columns (e.g., do not load password hashes or large text content into list views).
3. **Database Migrations:**
   - Schema modifications must be accompanied by both `up` and `down` migration scripts. Never modify production database schemas manually.

---

## 4. AI & Third-Party Service Resilience

1. **Groq LLaMA 3.3 Integration:**
   - Enforce system prompt boundaries to restrict the tutor to IT educational topics.
   - Validate and block prompt-injection keywords (`ignore previous instructions`, `developer mode`, etc.).
   - Wrap all external AI calls in `try...catch` blocks with graceful fallbacks: if Groq is unavailable, return an informative fallback message without crashing the user session.
