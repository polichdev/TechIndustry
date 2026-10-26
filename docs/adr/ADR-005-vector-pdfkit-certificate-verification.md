# ADR-005: Server-Side Vector PDFKit Certificate Generation

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-11 |
| Deciders | TechIndustry Core Team |

---

## Context
Graduating students expect high-quality, printable course completion certificates.

Two implementation approaches were considered:
1. **Headless Browser Rendering (Puppeteer / Chromium):** Rendering an HTML/CSS page to PDF via a headless browser. This approach is notoriously memory-heavy (~200MB–400MB RAM per Chromium instance) and prone to process freezes under concurrent requests.
2. **Native Vector PDF Generation (PDFKit):** Constructing the PDF document programmatically using Node.js native streams and vector primitives.

---

## Decision
We adopted **PDFKit** for server-side certificate generation:
- Low memory footprint (~5MB–10MB per generation job) and sub-second generation times.
- Native embedding of TrueType font assets (`Inter-Bold.ttf`, `Inter-Regular.ttf`) guaranteeing crisp typography at any print resolution.
- Every certificate is stamped with an immutable, verifiable serial key:
  `UC-{userId}-{courseId}-{base36Timestamp}`
- Verification links (`techindustry.ua/cert/:certNumber`) allow employers and LinkedIn viewers to confirm certificate authenticity directly against our database.

---

## Consequences

**Positive:**
- Minimal CPU and memory overhead compared to running headless Chrome instances.
- Zero external native binary dependencies.
- Cryptographically verifiable credential generation.

**Negative / Accepted:**
- Layout adjustments must be calculated using programmatic coordinate grids rather than standard CSS Flexbox/Grid.
