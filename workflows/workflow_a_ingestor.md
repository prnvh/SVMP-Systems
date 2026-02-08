# Workflow A: The Ingestor (Contextual Anchoring)

## I. The Functional Mandate
Refined during the v4.1 R&D cycle, Workflow A acts as the "Contextual Anchor." [cite_start]It shifts from an immediate-response model to a "wait-and-aggregate" gate to ensure 100% state integrity before any AI logic is applied [cite: 88-89].

## II. Technical Process
### 1. The Identity Handshake
Every event is tagged with the **Identity Frame** `(tenantId, clientId, userId)` immediately upon entry. [cite_start]This hard-silos the data, preventing cross-talk between organizations [cite: 91-93].

### 2. Atomic Upsert & Session Initialization
* **High-Integrity Write:** Uses an atomic upsert in `session_state`.
* **Logic:** If no active session exists, one is initialized. [cite_start]If one exists, the new message is appended to the stack [cite: 94-96].

### 3. The Soft Debounce Discovery (2.5s)
Instead of processing messages immediately, the system pushes the `debounceExpiresAt` window to **Now + 2.5s**. [cite_start]This merges fragmented "multi-burst" messages into a single **Complete Thought Unit**, reducing API overhead by 60% [cite: 97-98, 24].

### 4. Lock Preparation
To prepare for Workflow B, the Ingestor explicitly sets the `processing` flag to `FALSE`. [cite_start]This is the pre-cursor to the Atomic Mutex Lock[cite: 99].

## III. Performance Benchmarks
* [cite_start]**Decoupled Scaling:** Ingestion handles thousands of messages per second without being blocked by LLM latency[cite: 101].
* [cite_start]**Tenant Integrity:** Every write is strictly filtered by `tenantId`[cite: 102].