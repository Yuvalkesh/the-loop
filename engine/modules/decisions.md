# Module: Decisions

Logs decisions that were actually reached in a conversation, so they don't evaporate after the call.

**Fires when:** the classified type's `outputs` includes `decisions` AND `outputs.decisions.enabled` is true.
Common on internal syncs, delivery sessions, and partner working sessions.

`ROOT` = `storage.root`.

## 1. Identify real decisions
A decision is a **commitment to a course of action**, not a topic discussed. "We talked about pricing" is
not a decision. "We're moving to annual billing starting Q3" is. Capture only resolved ones; note open
questions separately if useful, but don't log them as decisions.

## 2. Write
For each decision, write `ROOT/decisions/<YYYY-MM-DD>-<slug>.md` from `templates/decision.md`:
- **What was decided** (one clear sentence)
- **Context / why** (the reasoning)
- **Owner** + any **deadline**
- **Source** — the transcript slug + timestamp

If a decision **reverses or supersedes** an earlier one, link the old decision file and note the change
rather than silently contradicting it.

## 3. Backlink
Add each decision path to the asset's **Spawns:** section.
