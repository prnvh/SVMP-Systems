# SVMP v4.1 | n8n Orchestration Logic (Production)

## Workflow A: The Ingestor (Atomic Write)
- **Role:** High-frequency ingestion & Soft Debounce.
- **Debounce Formula:** `debounceExpiresAt = Current_Time + 2.5s`.
- **Constraint:** This workflow never triggers an LLM; it only maintains state.
- **Identity Enforcement:** Incoming payloads MUST contain `{ tenantId, domainId, userId }`.

## Workflow B: The Processor (The Logic Fork)
- **Role:** Intent matching and AI synthesis.
- **Mutex Lock:** Immediately sets `processing: true`.

### Logic Fork (Domain-Aware Routing)
All execution paths are first routed using `identity_tuple.domainId`.

- **[ECOM]** → Use E-commerce databases, order services, and ECOM-scoped vector indexes
- **[D2C]** → Use Direct-to-Consumer databases, CRM services, and D2C-scoped vector indexes

No workflow step may access resources outside its assigned domain tag.


### The Logic Fork (Intent Bifurcation)
#### Path A: Transactional (Hard Logic)
- **Trigger:** Input contains Order Keywords (regex: `/#?(\d{4,})`).
- **Action:** Bypass LLM. Execute HTTP Request to Client ERP.
- **Confidence:** 100% (Deterministic).

#### Path B: Informational (Soft Logic)
- **Trigger:** No transactional intent detected.
- **Action:** Execute RAG Pipeline (Vector Search).
- **Critical Constraint (Multi-Cluster Isolation):**
  - Vector Search Query **MUST** filter by: 
    ```json
    { "tenantId": session.tenantId, "domainId": session.domainId }
    ```
  - *This prevents Sales queries from retrieving Support documents.*

- **Governance Gate:**
  - If Similarity Score ≥ 0.75 → Generate Answer.
  - If Similarity Score < 0.75 → **ESCALATE** to Human Agent.