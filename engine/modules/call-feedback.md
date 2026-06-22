# Module: Call Feedback

A private scorecard on **how the call was run** — plus an upsell map. For the person who ran the call to
get better at running calls. Internal only: real names kept, never published.

**Fires when:** the classified type's `outputs` includes `call-feedback` AND `outputs.call_feedback.enabled`
is true. Best on sales / client / relationship calls.

`ROOT` = `storage.root`.

## Write
`ROOT/lessons/_call-feedback/<YYYY-MM-DD>-<slug>.md` (create the folder if missing) with these sections:

1. **Scorecard** — fixed 5 dimensions so calls compare over time. Verdict ✅/⚠️/❌ + one line of evidence each:
   - Agenda control
   - Discovery / listening
   - Value framing
   - Pricing / anchoring
   - Next-step lock-in
2. **Upsell map** — table: opportunity → evidence quote (timestamp) → product shape → suggested price anchor → next move.
3. **What was good** — 3-6 bullets with quotes.
4. **What was missed** — 3-6 bullets with quotes.
5. **The ONE move for the next call** — a single concrete action, never a list of options.

**Quality bar:** honest over polite. If the call was clean, say so briefly — don't manufacture criticism.

This module never writes to a published brief and never anonymizes (it's private), so it ignores
`anonymization`. Add the file to the asset's **Spawns:** section.
