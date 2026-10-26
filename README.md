# TechIndustry — Interactive IT Learning Platform LMS

[![REST API](https://img.shields.io/badge/REST_API-Documentation-6BA539.svg?logo=openapiinitiative&logoColor=white)](docs/API_DOCUMENTATION.md)
[![Architecture: Layered MVC](https://img.shields.io/badge/Architecture-Layered_MVC-blue.svg)](docs/ARCHITECTURE.md)
[![Threat Model](https://img.shields.io/badge/Security-STRIDE_Threat_Model-red.svg)](docs/THREAT_MODEL.md)
[![ADR Records](https://img.shields.io/badge/ADR-Architecture_Decisions-purple.svg)](docs/adr/README.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[![Node.js](https://img.shields.io/badge/Node.js-20.x-339933.svg?logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Express.js 5](https://img.shields.io/badge/Express.js-5.x-000000.svg?logo=express&logoColor=white)](https://expressjs.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791.svg?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Sequelize](https://img.shields.io/badge/Sequelize-6.x_ORM-52B0E7.svg?logo=sequelize&logoColor=white)](https://sequelize.org/)
[![Handlebars](https://img.shields.io/badge/Handlebars-SSR_Engine-f0772b.svg?logo=handlebarsdotjs&logoColor=white)](https://handlebarsjs.com/)
[![JWT](https://img.shields.io/badge/JWT-Stateless_Auth-000000.svg?logo=jsonwebtokens&logoColor=white)](https://jwt.io/)
[![Google OAuth 2.0](https://img.shields.io/badge/Google-OAuth_2.0-4285F4.svg?logo=google&logoColor=white)](https://developers.google.com/identity)
[![Groq AI LLaMA 3.3](https://img.shields.io/badge/AI-Groq_LLaMA_3.3-F05032.svg?logo=openai&logoColor=white)](https://groq.com/)
[![PDFKit](https://img.shields.io/badge/PDFKit-Certificate_Engine-E0234E.svg)](https://pdfkit.org/)
[![Helmet Security](https://img.shields.io/badge/Helmet-CSP_%26_HSTS-000000.svg)](https://helmetjs.github.io/)
[![Azure Web App](https://img.shields.io/badge/Azure-Web_App_Hosting-0078D4.svg?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)

---

## Overview

**TechIndustry** is a scalable, modern Learning Management System (LMS) and IT education platform designed for interactive studying, real-time knowledge verification, and community gamification. Built with a robust **Layered MVC Architecture** on **Node.js (Express 5)**, **PostgreSQL (Sequelize ORM)**, and **Handlebars SSR**, the platform delivers structured learning tracks across modern software engineering domains (Frontend, Backend, DevOps, QA, Data Science, and Cybersecurity).

The platform features an automated assessment system with instant quiz evaluation, dynamic cryptographic and vector-rendered **PDF certificate generation**, an intelligent **Groq-powered AI tutor (LLaMA 3.3)** with topic guardrails, a full virtual economy with **XP, levels, badges, and cosmetic customization**, and an interactive in-browser **Code Sandbox**.

<p align="center">
  <img src="assets/screenshots/home.jpg" alt="TechIndustry Platform Overview" width="100%" />
</p>

---

## Tech Stack

### Backend & Core Engine — `src/`
- **Node.js 20.x** & **Express.js 5.x**: High-performance HTTP server and asynchronous request handling
- **Layered MVC Architecture**: Strict modular separation of Routes, Controllers, Services, Middleware, and Models
- **PostgreSQL 16 & Sequelize ORM**: Relational database persistence with schema associations and transaction management
- **JWT & Session Security**: Stateless JWT token authentication paired with secure session cookie context caching
- **Google OAuth 2.0 SSO**: Multi-provider authentication with automatic account linking
- **Security & Hardening Stack**:
  - **Helmet**: Content Security Policy (CSP) and HTTP Strict Transport Security (HSTS)
  - **CSURF**: Double-submit cookie CSRF defense for all mutation forms
  - **Express Rate Limit**: Tiered token-bucket rate limiters on sensitive endpoints (Auth, AI, Shop, Quizzes)
  - **Mongo Sanitize & HPP**: Defense-in-depth sanitization against NoSQL/parameter pollution attacks
  - **Input Validation**: Declarative request validation and XSS prevention via `express-validator` and `xss`
- **Groq AI Integration**: `llama-3.3-70b-versatile` AI mentor with strict domain prompts and anti-jailbreak filters
- **PDFKit Engine**: Dynamic vector rendering of verifiable completion certificates with custom typography
- **Sharp**: High-efficiency image processing, resizing, and avatar transformation pipeline
- **Nodemailer**: SMTP email verification, activation codes, and password recovery workflows
- **Compression**: Adaptive gzip response compression with threshold filtering

### Frontend & SSR View Layer — `src/views/` & `public/`
- **Handlebars (HBS) SSR**: Server-rendered dynamic views with reusable partials and custom helper functions
- **Modern Responsive UI**: Custom Glassmorphism styling with dark/light visual hierarchy and CSS variables
- **Dynamic Context Injection**: Real-time user session context, coin balances, and equipped avatar frames
- **Interactive Code Sandbox**: Client-side code editor for rapid coding experiments and algorithm testing
- **SEO & Social Optimization**: Dynamic OpenGraph tags, canonical links, structured sitemap.xml, and robots.txt
- **Zero-Friction Client UX**: Asynchronous JSON APIs with optimistic UI feedback and toast notifications

### Cloud & Deployment — `.github/workflows/`
- **Azure App Service**: Production cloud container runtime with auto-scaling
- **GitHub Actions CI/CD**: Automated deployment pipeline (`main_techindustry.yml`) on commit push
- **Cloud Database Management**: PostgreSQL connection pooling with SSL encryption options

---

## Core Features

- **Structured Course Curricula**: 13+ industry-aligned learning tracks (C#/.NET, Python, React, DevOps, QA, Kotlin, Java, Data Science, etc.) structured hierarchically into **Courses → Modules → Lessons**.
- **Markdown Lesson Engine**: Rich-text technical lesson rendering with syntax-highlighted code snippets and interactive navigation.
- **Automated Quiz & Assessment System**: Module-level quizzes with server-side validation, instant scoring, question shuffling, and immediate feedback.
- **Real-Time Progress Tracking**: Persistent course and lesson tracking in PostgreSQL with percentage calculations and status states (*Not Started*, *In Progress*, *Completed*).
- **Verifiable PDF Certificates**: Automated generation of branded, verifiable PDF certificates upon 100% course completion with inline preview and direct download.
- **Gamification & Virtual Economy**:
  - **XP & Levels Engine**: Experience points earned from lessons and quizzes with tiered rank progression (up to Level 1000+).
  - **Achievement Badges**: Automated unlocking of milestone badges (Level milestones, First Course, Multi-Course completions, Perfect Quizzes).
  - **Coins Economy**: Virtual currency awarded for learning actions with atomic transaction history (`CoinTransaction`).
  - **Global Leaderboard**: Competitive user ranking based on experience, level, and completed courses.
- **Shop & Profile Customization**: In-app cosmetics marketplace featuring avatar frames, title badges, and profile themes with equip/unequip functionality.
- **AI Learning Assistant (LLaMA 3.3)**: Built-in Groq AI mentor providing contextual explanations with anti-spam protections and domain restrictions (IT & platform only).
- **Interactive Code Sandbox**: Built-in coding playground for rapid experimentation without leaving the learning environment.
- **Enterprise-Grade Security**: Helmet headers, CSRF tokens, strict rate limiting, secure password hashing (Bcrypt), and PII protection.

---

## System Architecture & Patterns

TechIndustry follows a decoupled, layered MVC monolithic architecture with strict separation between routing, controllers, business services, and data models.

```
                    ┌─────────────────────────┐
                    │      Client Browser     │
                    └────────────┬────────────┘
                                 │ HTTP / HTTPS
                                 ▼
                    ┌─────────────────────────┐
                    │    Security & Armor     │
                    │  (Helmet, RateLimit,    │
                    │   CSRF, MongoSanitize)  │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │   Express 5 Dispatcher  │
                    └──────┬───────────┬──────┘
                           │           │
            ┌──────────────▼─────┐   ┌─▼──────────────────┐
            │    Page Routes     │   │     API Routes     │
            │    (SSR Engine)    │   │ (JSON Endpoints)   │
            └──────────────┬─────┘   └─┬──────────────────┘
                           │           │
                    ┌──────▼───────────▼──────┐
                    │  Controllers Layer      │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │  Business Services      │
                    │ (Gamification, AI,      │
                    │  Certificates, Auth)    │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │  Sequelize ORM Models   │
                    └────────────┬────────────┘
                                 │ SQL
                    ┌────────────▼────────────┐
                    │   PostgreSQL Database   │
                    └─────────────────────────┘
```

Detailed architectural specifications & request lifecycles: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

---

## Screenshots & Platform Tour

<table width="100%">
  <tr>
    <td width="50%" align="center">
      <b>Home Page</b><br/><br/>
      <img src="assets/screenshots/home.jpg" alt="Home Page" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>Course Catalog</b><br/><br/>
      <img src="assets/screenshots/courses.jpg" alt="Course Catalog" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <b>Course View & Lessons</b><br/><br/>
      <img src="assets/screenshots/course_view.jpg" alt="Course View" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>User Profile Dashboard</b><br/><br/>
      <img src="assets/screenshots/profile.jpg" alt="User Profile" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <b>Profile Achievements & Stats</b><br/><br/>
      <img src="assets/screenshots/profile_continuation_1.jpg" alt="Profile Achievements" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>Profile History & Badges</b><br/><br/>
      <img src="assets/screenshots/profile_continuation_2.jpg" alt="Profile Badges" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <b>Global Leaderboard</b><br/><br/>
      <img src="assets/screenshots/leaderboard.jpg" alt="Leaderboard" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>Quiz & Assessment Center</b><br/><br/>
      <img src="assets/screenshots/tests.jpg" alt="Quiz Center" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <b>Interactive Quiz View</b><br/><br/>
      <img src="assets/screenshots/tests_view.jpg" alt="Quiz View" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>Learning Roadmaps</b><br/><br/>
      <img src="assets/screenshots/roadmap.jpg" alt="Roadmaps" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <b>Q&A Knowledge Base</b><br/><br/>
      <img src="assets/screenshots/qa.jpg" alt="Q&A" width="100%"/>
    </td>
    <td width="50%" align="center">
      <b>Cosmetics Shop & Economy</b><br/><br/>
      <img src="assets/screenshots/shop.jpg" alt="Shop" width="100%"/>
    </td>
  </tr>
  <tr>
    <td width="100%" align="center" colspan="2">
      <b>Interactive Code Sandbox</b><br/><br/>
      <img src="assets/screenshots/sandbox.jpg" alt="Code Sandbox" width="50%"/>
    </td>
  </tr>
</table>

---

## Repository Structure

```
tech-industry/
├── .github/
│   └── workflows/
│       └── main_techindustry.yml        # CI/CD: Automated Azure Web App deployment pipeline
├── assets/
│   ├── fonts/                           # Custom web fonts (Inter Regular & Bold)
│   ├── img/                             # Platform branding, logo, and favicons
│   └── screenshots/                     # Platform preview images and UI screenshots
├── content/
│   └── courses/                         # 13+ Course curricula in structured Markdown & JSON
│       ├── csharp-dotnet/               # C# & .NET Core curriculum
│       ├── cybersecurity-engineering/   # Cybersecurity engineering track
│       ├── data-science-engineering/    # Data science & analytics track
│       ├── design-fundamentals/         # UI/UX design track
│       ├── devops-engineering/          # DevOps & cloud infrastructure track
│       ├── java-core/                   # Java core & enterprise track
│       ├── javascript-basics/           # JavaScript fundamentals track
│       ├── kotlin-core/                 # Kotlin core & Android track
│       ├── postgresql-and-mongodb/      # Database engineering track
│       ├── python-for-data/             # Python data & automation track
│       ├── qa-engineering/              # QA engineering & automation track
│       ├── react-next/                  # React & Next.js ecosystem track
│       └── typescript-core/             # TypeScript mastery track
├── docs/
│   ├── API_DOCUMENTATION.md             # Complete REST API specification & schemas
│   └── ARCHITECTURE.md                  # Comprehensive system architecture & patterns
├── public/                              # Static public assets (CSS stylesheets, JS, robots.txt, sitemap.xml)
├── src/
│   ├── config/                          # Database connection pool, rate limits, constants, content seeders
│   ├── controllers/                     # Route controllers (Auth, Courses, Quizzes, Certificates, Shop, etc.)
│   ├── helpers/                         # Custom Handlebars template helpers & formatters
│   ├── middleware/                      # Auth guards, page protection, SEO defaults, rate limiting
│   ├── models/                          # Sequelize models (User, Customer, Course, Module, Lesson, Progress, Shop)
│   ├── routes/                          # Express route declarations (REST API & SSR pages)
│   ├── services/                        # Domain business logic (AI, Gamification, Certificates, Progress, Shop)
│   └── views/                           # Handlebars view templates
│       ├── pages/                       # SSR page layouts (Home, Profile, Course, Quiz, Settings, Shop)
│       └── partials/                    # Reusable UI partials (Navbar, Footer, Sidebar, Cards, Modals)
├── server.js                            # Application entry point, security middleware pipeline & server bootstrap
├── package.json                         # Node.js dependencies, scripts, and package metadata
└── LICENSE                              # Project license (MIT)
```

---

## Running Locally

Follow these step-by-step instructions to run TechIndustry on your local environment:

### Prerequisites
- **Node.js**: `v20.x` or higher (LTS recommended)
- **npm**: `v10.x` or higher
- **PostgreSQL**: `v15.x` or `v16.x`

### 1. Clone the Repository
```bash
git clone https://github.com/polchduikt/TechIndustry.git
cd TechIndustry
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Environment Configuration
Create a `.env` file in the root directory:

```env
PORT=3000
NODE_ENV=development

# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=techindustry_db
DB_USER=postgres
DB_PASSWORD=your_password

# Authentication & Security
JWT_SECRET=your_super_secret_jwt_key_here
USER_CONTEXT_CACHE_MS=60000

# Google OAuth 2.0 (Optional)
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:3000/api/auth/google/callback

# Groq AI Service (Optional)
GROQ_API_KEY=your_groq_api_key

# Email SMTP Service (Optional)
EMAIL_USER=your_email@gmail.com
EMAIL_PASSWORD=your_app_password
```

### 4. Database Setup & Seeding
Ensure PostgreSQL is running and create the target database:

```sql
CREATE DATABASE techindustry_db;
```

Seed the courses and curriculum content:

```bash
node src/config/seeders/syncCoursesFromContent.js
node src/config/seeders/syncLessonsFromContent.js
```

### 5. Start Development Server
```bash
npm run dev
```

The application will be accessible at [http://localhost:3000](http://localhost:3000).

---

## Documentation & Engineering Index

All architectural choices, engineering standards, threat assessments, and developer guidelines are comprehensively documented:

### Architecture & Deep-Dive Specs
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) — System architecture, layered request lifecycles, and design patterns.
- [docs/DATA_MODEL.md](docs/DATA_MODEL.md) — Complete relational schema, Entity-Relationship (ER) diagram, indexing, and transactions.
- [docs/THREAT_MODEL.md](docs/THREAT_MODEL.md) — STRIDE security model, trust boundaries, and vulnerability mitigations.
- [docs/GAMIFICATION_ENGINE.md](docs/GAMIFICATION_ENGINE.md) — Level threshold formulas, streak mechanics, and coin ledger accounting.
- [docs/AI_MENTOR_PIPELINE.md](docs/AI_MENTOR_PIPELINE.md) — Groq LLaMA 3.3 pipeline, topic guardrails, and token limits.
- [docs/API_DOCUMENTATION.md](docs/API_DOCUMENTATION.md) — Complete REST API specification, request/response schemas, and rate limits.
- [docs/DEPLOYMENT_AND_OPERATIONS.md](docs/DEPLOYMENT_AND_OPERATIONS.md) — Production deployment with Nginx, PM2, Docker, and PostgreSQL tuning.

### Architecture Decision Records (ADRs)
Explore our rationale and trade-offs in [docs/adr/](docs/adr/README.md):
- [ADR-001: Modular Layered MVC Monolith](docs/adr/ADR-001-layered-mvc-monolith.md)
- [ADR-002: PostgreSQL and Sequelize ORM](docs/adr/ADR-002-postgresql-and-sequelize.md)
- [ADR-003: Hybrid Session and JWT Authentication](docs/adr/ADR-003-hybrid-session-and-jwt-auth.md)
- [ADR-004: Groq LPU LLaMA 3.3 for AI Mentorship](docs/adr/ADR-004-groq-llama3-for-ai-mentorship.md)
- [ADR-005: Server-Side Vector PDFKit Certificate Generation](docs/adr/ADR-005-vector-pdfkit-certificate-verification.md)
- [ADR-006: Multi-Layered Defense-in-Depth Security Pipeline](docs/adr/ADR-006-defense-in-depth-security-pipeline.md)

### AI Agent & Developer Standards
- [agent/RULES.md](agent/RULES.md) — Mandatory development rules, transaction invariants, and coding standards.
- [agent/ARCHITECTURE.md](agent/ARCHITECTURE.md) — Fast-load mental model and directory responsibilities.
- [agent/TECH_STACK.md](agent/TECH_STACK.md) — Detailed library version matrix and rationale.
- [agent/ROADMAP.md](agent/ROADMAP.md) — Completed milestones, active initiatives, and future vision.

### Community & Governance
- [CONTRIBUTING.md](CONTRIBUTING.md) — Contribution guidelines, local environment setup, and Conventional Commits.
- [SECURITY.md](SECURITY.md) — Vulnerability disclosure policy and security SLAs.
- [CHANGELOG.md](CHANGELOG.md) — Release notes and version history adhering to SemVer.
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) — Contributor Covenant v2.1.
- [LICENSE](LICENSE) — Project licensing terms (MIT License).

---

## Status

TechIndustry is actively maintained, continuously updated with new course tracks, gamification mechanics, and architectural enhancements.

---

## License

This project is licensed under the [MIT License](LICENSE).
