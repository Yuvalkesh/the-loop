# Module: Teaching Lessons

Mines reusable lessons from a conversation — knowledge worth keeping for the team or audience.

**Fires when:** the classified type's `outputs` includes `teaching-lessons` AND
`outputs.teaching_lessons.enabled` is true. Densest on delivery sessions, workshops, and mentoring.

`ROOT` = `storage.root`.

## 1. Scan for lesson candidates
Re-read the transcript looking for any of:
- **friction** — something that blocked or confused someone (setup, conceptual, process)
- **metaphor** — language that made an abstract idea click
- **governance** — a security / permissions / approval question that came up
- **tooling** — a tool or workflow tradeoff someone surfaced or worked around
- **pedagogy** — what worked or fell flat when explaining something
- **gap** — a topic that couldn't be fully addressed (drives the next iteration)

## 2. Quality bar
A lesson must be **reusable** — true across situations, not a one-off. "This person was confused" does not
qualify. "People consistently confuse X with Y" does. Often a transcript yields zero. That's fine — record
"0 lessons" rather than forcing entries.

## 3. Write / update
For each candidate, check `ROOT/lessons/` for an existing slug:
- **Exists** → update it: bump `last_observed`, append a citation. Don't duplicate.
- **New** → write `ROOT/lessons/<slug>.md` from `templates/lesson.md` with `source_event: <transcript slug>`.

## 4. Backlink
Add each lesson path to the asset's **Spawns:** section.
