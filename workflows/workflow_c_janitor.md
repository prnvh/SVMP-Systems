# Workflow C: The Janitor (Data Lifecycle Management)

## I. The Functional Mandate
Workflow C acts as the automated "garbage collector" that maintains system leanness and ensures GDPR compliance by removing stale data.

## II. Technical Process (24h Cycle)
1. **Temporal Trigger:** Runs on a nightly 24-hour Cron cycle.
2. **Scan & Purge:** Identifies session documents in `session_state` that have not been updated in the last 24 hours.
3. **Governance Finalization:** Before deletion, the final conversation state is mirrored to the **Governance Ledger** to preserve an immutable forensic record.

## III. Outcome
* **Privacy:** "Closed" conversations do not persist indefinitely.
* **Performance:** Keeps the active session collection small for faster query times.