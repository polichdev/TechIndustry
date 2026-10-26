# ADR-004: Groq LPU LLaMA 3.3 for Conversational AI Tutoring

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-09-08 |
| Deciders | TechIndustry Core Team |

---

## Context
Interactive learning requires an AI mentor that responds instantly when a learner asks a question while debugging code or studying theory.

Traditional cloud GPU providers (running OpenAI GPT-4 or Anthropic Claude) typically exhibit a Time-To-First-Token (TTFT) latency of $1.5\text{s} - 3.5\text{s}$, producing perceptible delays that disrupt student focus. Furthermore, API token costs at scale can be substantial.

---

## Decision
We integrated **Groq Cloud API** running `llama-3.3-70b-versatile` as the primary inference engine:
- Groq's custom LPU (Language Processing Unit) architecture delivers generation speeds exceeding 300 tokens per second with TTFT under 250 milliseconds.
- We utilize the OpenAI-compatible SDK interface (`baseURL: "https://api.groq.com/openai/v1"`), ensuring clean interoperability and trivial swapping of backend providers.
- Strict input keyword and regex checks (`isBlockedRequest`) short-circuit prompt injection attacks prior to reaching the Groq API.
- Google Gemini and OpenAI are retained in `package.json` as hot fallbacks should Groq experience capacity limits.

---

## Consequences

**Positive:**
- Near-instantaneous response generation gives learners the feel of a real-time conversational partner.
- Cost-effective high-token throughput.
- Zero vendor lock-in due to standardized OpenAI chat completion request structures.

**Negative / Accepted:**
- Strict rate limits on free/starter tiers require robust backoff and error handling in `aiService.js`.
