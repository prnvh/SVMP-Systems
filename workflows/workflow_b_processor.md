# Workflow B: The Processor (Reasoning Engine)

## I. The Functional Mandate
Workflow B serves as the primary reasoning layer. [cite_start]It transitions sessions from 'Pending' to 'Resolved' using a **Cron-based trigger** and **Exactly-Once** processing architecture[cite: 122].

## II. Technical Process
### 1. Cron Trigger & Atomic Locking
* [cite_start]**Trigger:** Scans for documents where `debounceExpiresAt <= now` and `processing = false`[cite: 124].
* **MUTEX Lock:** Immediately sets `processing: true`. [cite_start]This prevents race conditions, ensuring a user never receives a double response even under high load [cite: 125-126].

### 2. Contextual Aggregation
[cite_start]It compiles the "Complete Thought Unit" by merging all text fragments captured during the 2.5s debounce window into a single coherent query string[cite: 127].

### 3. The Intent Logic Fork (v4.1)
The system evaluates the intent before calling the LLM:
* [cite_start]**Transactional Path:** If the intent requires data (e.g., "Order Status"), it bypasses the LLM and triggers a direct API call to Shopify/ERP [cite: 128-129].
* [cite_start]**Informational Path:** For general queries, it filters the Knowledge Base by `domainId` and applies the **≥0.75 Similarity Gate**[cite: 130].

## III. Zero-Trust Governance
* [cite_start]**Hard Escalation:** If the Similarity Score < 0.75, the system freezes and alerts a human agent via Slack [cite: 133-134].
* [cite_start]**Audit Logging:** Every decision (Score, Logic Branch, API Response) is written to the immutable **Governance Ledger**[cite: 135].