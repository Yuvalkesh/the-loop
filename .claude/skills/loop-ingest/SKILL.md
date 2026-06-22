---
name: loop-ingest
description: Digest a meeting/sales/client/interview/podcast transcript into structured business assets — CRM records, content posts, teaching lessons, decisions and action items — routed per your loop.config.yaml. Use when the user pastes a transcript, points to a transcript file path or URL, says "ingest this", "run the loop on this", "digest this call", "/loop-ingest", or "add this transcript". If no loop.config.yaml exists yet, run /loop-onboard first.
---

# The Loop — Ingest

The config-driven transcript engine. Turns one conversation into the assets your business actually uses.

## Procedure
1. **Load config.** Read `loop.config.yaml` at the repo root.
   - If it does NOT exist, stop and tell the user to run `/loop-onboard` first.
2. Read `engine/ingest-workflow.md` and follow it exactly, using the loaded config for:
   - the user's own `call_types` taxonomy (how to classify the transcript),
   - which output modules fire for that type (`outputs:` per type),
   - the CRM backend (`outputs.crm.backend`, default `markdown`),
   - the storage root (`storage.root`, default `./loop`),
   - anonymization rules and git behavior.
3. Each enabled output module lives in `engine/modules/<name>.md` — read the module file before running it.

## Repo root
The directory containing `loop.config.yaml`.
