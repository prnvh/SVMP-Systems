# Workflow C: The Janitor (Lifecycle Management)

## Overview
A scheduled maintenance workflow to enforce data hygiene.

**Trigger:** Cron (Every 24 Hours at 00:00 UTC)
**Target:** `session_state` collection

## The Cleaning Cycle
1.  **Identify:** Find sessions where `status` is "processed" AND `updatedAt` is older than 24 hours.
2.  **Archive:** Copy these sessions to the `governance_ledger` (Cold Storage).
3.  **Purge:** Delete them from `session_state` (Hot Storage).