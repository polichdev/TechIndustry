# TechIndustry — Relational Data Model & Database Architecture

| Metadata | Specification |
|---|---|
| Target Database | PostgreSQL 14+ |
| ORM | Sequelize 6.37.x |
| Schema Strategy | Normalized Relational (3NF) with JSONB flexibility |
| Transaction Isolation | Read Committed (default) / Serializable for Coin Mutations |

---

## 1. Entity-Relationship Overview

```mermaid
erDiagram
    USERS ||--o| CUSTOMERS : "has profile"
    USERS ||--o| USER_LEVELS : "tracks progression"
    USERS ||--o{ USER_PROGRESS : "records completion"
    USERS ||--o{ USER_PURCHASES : "owns items"
    USERS ||--o{ COIN_TRANSACTIONS : "ledger history"

    COURSES ||--o{ MODULES : "contains"
    MODULES ||--o{ LESSONS : "groups"
    LESSONS ||--o{ USER_PROGRESS : "tracked in"

    SHOP_CATEGORIES ||--o{ SHOP_ITEMS : "categorizes"
    SHOP_ITEMS ||--o{ USER_PURCHASES : "purchased via"

    USERS {
        int id PK
        string username
        string email UK
        string password
        int xp
        int coins
        string role
        datetime createdAt
    }

    CUSTOMERS {
        int id PK
        int userId FK
        string firstName
        string lastName
        string avatar
        string bio
    }

    USER_LEVELS {
        int id PK
        int userId FK
        int currentLevel
        int totalXp
        jsonb badges
        datetime updatedAt
    }

    COURSES {
        int id PK
        string slug UK
        string title
        text description
        string level
        int durationHours
        boolean published
    }

    MODULES {
        int id PK
        int courseId FK
        string title
        int order
    }

    LESSONS {
        int id PK
        int moduleId FK
        string slug
        string title
        text contentPath
        int xpReward
        int coinsReward
        int order
    }

    USER_PROGRESS {
        int id PK
        int userId FK
        int lessonId FK
        boolean completed
        datetime completedAt
    }

    SHOP_ITEMS {
        int id PK
        int categoryId FK
        string name
        int priceCoins
        string itemType
        jsonb metadata
        boolean active
    }

    USER_PURCHASES {
        int id PK
        int userId FK
        int itemId FK
        int pricePaid
        datetime purchasedAt
    }

    COIN_TRANSACTIONS {
        int id PK
        int userId FK
        int amount
        string type
        string description
        datetime createdAt
    }
```

---

## 2. Model Specifications & Constraints

### 2.1 Users & Identity
- **`Users`**: Primary authentication entity. Passwords hashed using `bcrypt` (12 salt rounds).
- **`Customers`**: Extended user profile. Contains personal presentation data, customizable titles, and social handles.
- **`UserLevels`**: Stores pre-computed level status, total XP, and an array of unlocked badge identifiers stored in a `JSONB` column for fast array lookups without joining a separate badge join table for simple read operations.

### 2.2 Curricula Hierarchy
- **`Courses` $\rightarrow$ `Modules` $\rightarrow$ `Lessons`**:
  - `Courses.slug` is indexed uniquely for human-readable and SEO-friendly URLs (`/courses/csharp-dotnet`).
  - `Lessons.order` and `Modules.order` enforce linear or semi-linear pedagogical progression.
- **`UserProgress`**:
  - Unique composite index on `(userId, lessonId)`.
  - Upsert pattern used when marking lessons complete to prevent duplicate record insertion.

### 2.3 Financial & Gamification Ledger
- **`CoinTransactions`**:
  - Append-only financial ledger.
  - Every coin movement (positive for rewards, negative for shop purchases) generates an immutable record.
  - Summing `CoinTransactions` for a given `userId` matches the balance stored on `Users.coins`, serving as an audit trail against race conditions and tampering.

---

## 3. Transaction Management & Concurrency Control

When purchasing items from the shop or completing milestone courses:
1. An explicit managed transaction is acquired: `await db.sequelize.transaction(...)`.
2. A row-level lock (`SELECT ... FOR UPDATE`) is optionally placed on the user row if high contention is expected.
3. User coin balance is verified (`user.coins >= item.priceCoins`).
4. User balance is decremented, `UserPurchase` is inserted, and a `CoinTransaction` is logged atomically.
5. If any step fails, the transaction automatically rolls back, guaranteeing zero currency leakage.

---

## 4. Indexing & Query Optimization Strategy

| Table | Index Columns | Type | Purpose |
|---|---|---|---|
| `Users` | `email` | UNIQUE B-Tree | O(1) login lookup. |
| `Courses` | `slug` | UNIQUE B-Tree | Course detail route dispatch. |
| `UserProgress` | `(userId, lessonId)` | UNIQUE Composite | Prevents double completions; fast check for lesson access. |
| `UserProgress` | `(userId, completed)` | Composite B-Tree | Rapid progress bar and certificate eligibility calculations. |
| `CoinTransactions` | `userId, createdAt` | Composite B-Tree | Paginated ledger history for user profile. |
| `UserLevels` | `totalXp DESC` | B-Tree | High-speed global leaderboard ranking. |
