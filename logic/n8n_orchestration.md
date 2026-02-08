# n8n Orchestration Logic (v4.1)

## 1. The Intent Logic Fork
In v4.1, the system implements a **Conditional Execution Pipeline** to separate concerns before the LLM is involved.

### Step 1: Database-Level Trigger
[cite_start]The workflow begins by checking the `tenants` collection for specific tags[cite: 292]:
* [cite_start]**[ECOM] / [D2C]:** Activates the High Precision Intent Matcher[cite: 293].
* [cite_start]**Standard:** Routes directly to the v4.0 Similarity Gate (RAG)[cite: 294].

### Step 2: The Binary Node
For tagged tenants, the system evaluates the query:

#### A. Transactional Path (Deterministic)
* [cite_start]**Trigger:** Keywords like "Tracking", "Order", "Cancel"[cite: 296].
* **Action:** 1. Checks `session_state` for a validated `orderId`.
  2. [cite_start]**Bypasses LLM:** Makes a direct API call to Shopify/ERP[cite: 297].
* [cite_start]**Outcome:** Zero hallucination for logistics/status queries[cite: 300].

#### B. Informational Path (Probabilistic)
* **Trigger:** General inquiries (e.g., "How do I return?", "Shipping policy").
* **Action:**
  1. [cite_start]Filters Knowledge Base by `domainId`[cite: 298].
  2. [cite_start]Applies **Similarity Gate >= 0.75**[cite: 298].
* **Outcome:** RAG-based response or Human Escalation if confidence is low.

## 2. Global Invariants
* [cite_start]**Mutex Locking:** All flows respect the `processing: true` lock to prevent race conditions[cite: 233].
* [cite_start]**Governance Logging:** Every decision (Logic Branch A vs B) is written to the immutable ledger[cite: 281].