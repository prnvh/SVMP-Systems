# SVMP Systems v4.1
### State-Aware, Multi-Tenant Governance Engine for LLM-Based Systems

SVMP Systems is a reference implementation of a state-aware orchestration layer designed to address reliability failures in LLM-driven applications. Rather than focusing on prompt optimization or model tuning, the system addresses failure at the systems and governance layer.

---

## Problem Statement
Most failures in LLM deployments are not caused by incorrect model outputs, but by loss of contextual integrity under concurrency.

**Common failure modes include:**
- Fragmented user messages (“multi-burst” behavior)
- Race conditions under parallel processing
- Hallucinated responses under low confidence
- Cross-tenant data exposure in shared systems

SVMP addresses these issues structurally by treating LLMs as probabilistic components that must be governed, not trusted implicitly.

---

## Core Design Invariants
SVMP v4.1 is built around the following non-negotiable constraints:

- **State-First Architecture:** Conversational state is persisted and resolved asynchronously.
- **Exactly-Once Processing:** No session can be processed more than once (Mutex Locking).
- **Soft Debounce Aggregation:** Fragmented user messages are aggregated into a single “thought unit” (2.5s window).
- **Hard Multi-Tenant Isolation:** Enforced at the database level via an Identity Tuple (`tenantId`, `userId`).
- **Intent Bifurcation:** Transactional queries (e.g., Orders) bypass the LLM entirely; only informational queries use the LLM.

---

## Repository Structure
This repository focuses on architecture, invariants, and failure containment rather than UI or deployment tooling.

- `ARCHITECTURE.md` — system-level design and concurrency model
- `FAILURE_CASES.md` — known failure modes and structural mitigations
- `logic/` — decision engines, orchestration, and intent bifurcation
- `workflows/` — maintenance cycles (Janitor) and cron jobs
- `schemas/` — session, governance, and identity data models

---

## Architectural Evolution (Whitepapers)

### v4.1 Whitepaper (Implemented)
**Status:** Production-Grade
**Focus:** Intent Bifurcation & Forensic Auditability
- **The Logic Fork:** Structural separation of Transactional (Deterministic) and Informational (Probabilistic) flows.
- **Identity Locking:** Strict isolation enforced via Identity Tuples.
- **Lifecycle Hygiene:** Automated "Janitor" workflows ensuring zero-retention in hot storage (>24h).

### v4.0 Whitepaper (Conceptual Foundation)
**Status:** Theoretical Baseline
**Focus:** Session-Centricity
- Defined the move from "Message-Centric" to "Session-Centric" architecture.
- Established the "Soft-Debounce" pattern for multi-burst handling.

### v3.0 Whitepaper (Legacy)
**Status:** Historical Reference
**Focus:** Message-Centric
- Best-effort orchestration with guardrails.
- Retained here to document the rationale for the v4 rewrite.

---

## Project Status
SVMP v4.1 represents a production-oriented architecture validated through iterative design and adversarial reasoning.

## Author
**Pranav H**
Lead Architect — SVMP Systems