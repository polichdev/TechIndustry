# Contributing to TechIndustry

Thank you for your interest in contributing to **TechIndustry**! As a scalable IT education platform and LMS, maintaining high standards of code quality, architecture integrity, security, and performance is our primary mission.

This document outlines the guidelines and procedures for contributing to this codebase.

---

## 1. Code of Conduct

All contributors and maintainers are expected to adhere to our [Code of Conduct](CODE_OF_CONDUCT.md). Please report unacceptable behavior according to our contact guidelines.

---

## 2. Development Setup

### Prerequisites
- **Node.js**: v18.x or v20.x LTS (Node.js 22 supported)
- **npm**: v9+
- **PostgreSQL**: v14+ (local instance or Docker container)

### Step-by-Step Installation
1. **Fork and clone** the repository:
   ```bash
   git clone https://github.com/polchduikt/TechIndustry.git
   cd tech-industry
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Configure Environment Variables**:
   Copy the sample environment file and configure your local settings:
   ```bash
   cp .env.example .env # or edit .env
   ```
   Ensure you provide valid values for:
   - `PORT`: default `3000`
   - `DATABASE_URL` or `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`, `DB_PORT`
   - `SESSION_SECRET`: cryptographic random string
   - `JWT_SECRET`: cryptographic random string
   - `GROQ_API_KEY`: Groq Cloud key for LLaMA 3.3 mentorship
   - `SMTP_*`: local or test credentials (e.g. Mailtrap)

4. **Initialize Database**:
   ```bash
   npx sequelize-cli db:migrate
   npx sequelize-cli db:seed:all
   ```

5. **Start Development Server**:
   ```bash
   npm run dev
   ```
   The platform will be available at `http://localhost:3000`.

---

## 3. Branching & Git Workflow

We adhere to a Trunk-Based / Feature-Branch workflow:
- `main`: Production-ready branch. Direct pushes to `main` are restricted.
- Feature branches: `feat/<short-description>` (e.g., `feat/course-quizzes`)
- Bugfix branches: `fix/<short-description>` (e.g., `fix/csrf-token-expiration`)
- Refactoring branches: `refactor/<short-description>`
- Documentation: `docs/<short-description>`

### Commit Message Guidelines (Conventional Commits)
All commit messages must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <short imperative summary>

[optional body describing the motivation and architectural context]

[optional footer(s) like Closes #123]
```

**Common types:**
- `feat`: A new feature for the user or LMS learner
- `fix`: A bug fix in controllers, services, or models
- `docs`: Documentation updates or additions
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: Code change that improves database query performance or caching
- `security`: Security-related changes (CSP, rate-limiting, auth)
- `test`: Adding or updating unit/integration tests

---

## 4. Engineering Standards & Architectural Invariants

Before submitting code, ensure you have complied with the core architectural rules detailed in [`agent/RULES.md`](agent/RULES.md):

1. **Transactional Integrity**: Any mutation that modifies multiple database tables (e.g., leveling up, coin deduction, course purchase, certificate issuing) **must** run inside a managed Sequelize transaction (`db.sequelize.transaction`).
2. **Security & Input Validation**:
   - All incoming HTTP payloads must be validated via `express-validator` middleware before touching service layers.
   - Do not pass raw SQL concatenation into queries. Use ORM binding or `Sequelize.literal` with parameterized arrays.
   - All user-generated content rendered in SSR must pass through `xss` filters and Handlebars safe escaping.
3. **No Business Logic in Controllers**: Controllers must only unpack HTTP requests, call domain services, and return responses (JSON or Handlebars render).

---

## 5. Submitting a Pull Request (PR)

1. Ensure all linter rules and tests pass cleanly without errors or warnings.
2. Verify database backward compatibility: Ensure any Sequelize migrations can be applied without downtime and include corresponding down migrations.
3. Open a Pull Request against `main` using our [Pull Request Template](.github/PULL_REQUEST_TEMPLATE.md).
4. Provide a clear summary of changes, screenshot/video for UI changes, and list the affected services/models.
5. At least one review approval is required before merging.
