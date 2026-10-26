# TechIndustry — Agent Architecture Summary

This document provides a concise architectural mental model for AI coding agents and automated workflows working on the **TechIndustry** repository.

---

## 1. Top-Level Architectural Pattern

TechIndustry is designed as a **Modular Layered MVC Monolith**:

```mermaid
flowchart TD
    Client["Client Web Browser"] --> Security["Security & Defense Pipeline<br/>(Helmet, RateLimit, CSRF, HPP)"]
    Security --> Router["Express 5 Router"]
    Router --> Controllers["Controllers (HTTP Dispatchers)"]
    Controllers --> Services["Domain Business Services"]
    Services --> Models["Sequelize ORM Models"]
    Models --> DB[(PostgreSQL Database)]
    Services --> External["External Integrations<br/>(Groq LLaMA 3.3, Nodemailer, PDFKit)"]
    Controllers --> Views["Handlebars SSR Views"]
    Views --> Client
```

---

## 2. Directory Map & Responsibilities

| Directory | Role & Constraints |
|---|---|
| `src/config/` | Database (`database.js`), security, and third-party configuration bindings. |
| `src/controllers/` | Request unpacking, DTO validation, view rendering, or JSON response dispatch. **No direct business logic or DB calls.** |
| `src/services/` | Core domain operations: `gamificationService`, `aiService`, `authService`, `courseService`, `progressService`, `shopService`, `certificateService`. All transactions live here. |
| `src/models/` | Sequelize model declarations, relations, and hooks (`User`, `Course`, `Module`, `Lesson`, `CoinTransaction`, etc.). |
| `src/middleware/` | Auth checks (`isAuthenticated`, `isAdmin`), CSRF, rate limiters, validation middlewares. |
| `src/helpers/` | Handlebars view helper functions (`eq`, `formatDate`, `levelProgress`, etc.). |
| `src/views/` | Handlebars templates (`.hbs`), partials (`partials/`), and master layouts (`layouts/`). |
| `content/courses/` | Markdown course curriculum lessons and structured syllabi. |
| `public/` & `assets/` | Static CSS, JS, fonts (Inter-Bold, Inter-Regular), and imagery. |

---

## 3. Critical Runtime Flows

### 1. Course Progression & Gamification Flow
```mermaid
sequenceDiagram
    autonumber
    actor Learner as User Browser
    participant Router as Express Router
    participant Ctrl as CourseController
    participant Serv as ProgressService / GamificationService
    participant DB as PostgreSQL (Sequelize)

    Learner->>Router: POST /api/lessons/:id/complete (with _csrf)
    Router->>Ctrl: Dispatch verified request
    Ctrl->>Serv: completeLesson(userId, lessonId)
    Note over Serv,DB: Open db.sequelize.transaction
    Serv->>DB: UserProgress.upsert({ completed: true })
    Serv->>DB: User.increment({ xp: +50, coins: +10 })
    Serv->>Serv: Recalculate User Level from XP_CONFIG
    Serv->>DB: UserLevel.update({ currentLevel })
    Serv->>DB: CoinTransaction.create({ amount: 10, type: 'LESSON_REWARD' })
    Note over Serv,DB: Commit Transaction
    Serv-->>Ctrl: { xpGained: 50, newLevel: 5, newBadges: [] }
    Ctrl-->>Learner: HTTP 200 JSON
```

### 2. AI Mentor Query Flow
```mermaid
sequenceDiagram
    autonumber
    actor Learner as User Browser
    participant Ctrl as AIController
    participant Serv as AIService
    participant Groq as Groq Cloud (LLaMA 3.3)

    Learner->>Ctrl: POST /api/ai/chat (userMessage, history)
    Ctrl->>Serv: generateResponse(userMessage, history)
    Serv->>Serv: Check isBlockedRequest(userMessage) (Jailbreak / Injection filter)
    alt Pattern Blocked
        Serv-->>Ctrl: Static off-topic response
    else Valid Educational Query
        Serv->>Groq: groq.chat.completions.create(model: "llama-3.3-70b-versatile")
        Groq-->>Serv: Streamed / completed tokens
        Serv-->>Ctrl: Sanitized educational advice
    end
    Ctrl-->>Learner: HTTP 200 JSON response
```
