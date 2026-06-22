# Workflow: Ingest

The config-driven engine. One transcript in → structured business assets out, routed by
`loop.config.yaml`. This is a portable, generic port of a battle-tested transcript pipeline:
the proven bones (read fully → classify → pause → immutable source → asset → typed spawns) are
kept; everything personal to the original author is replaced by config.

**Prerequisite:** `loop.config.yaml` exists. If not, tell the user to run `/loop-onboard`.

Throughout, `ROOT` = `storage.root` from config (default `./loop`).

---

## 1. Read the transcript fully
Don't skim. Note speakers, timestamps, key turns, and anything quoted verbatim worth keeping.
If the transcript is too long for one read, chunk it and read **all** chunks before classifying —
calls often pivot halfway (a "marketing call" becomes an ops-expansion call at minute 27).

**Intake checks:**
- **Date is mandatory before writing.** If the transcript states no date, ask ("assuming today — correct?").
- **Resolve the attendee roster** (who was on each side) before classifying.
- **Room-call speaker labels:** if one "speaker" appears to argue with themselves, a shared room mic merged
  voices — preserve labels verbatim, flag the unreliable sections, never silently re-attribute.

## 2. Classify the type
Match the transcript to one of the user's `call_types` from config. If filename/folder/context makes it
obvious, use that. **If ambiguous, ask the user** — guessing wrong routes the work to the wrong place.
If it fits no configured type cleanly, say so (it may mean their taxonomy needs a new type) rather than
force-fitting.

## 3. Detect anonymization needs
If the classified type is listed in `anonymization.types_requiring_anon` (or `anonymization.default` is
true), any spawned **post/brief** must strip company names and identifying industry detail. The source
transcript and CRM records keep real names (internal). Public types (podcast/interview the user published)
need no anonymization.

## 4. Surface a 3-line takeaway and PAUSE
Before writing any files, output:
- **One-line summary:** what this transcript is.
- **Classified type** + the **modules that will fire** for it (from the type's `outputs`).
- **Slug:** `<YYYY-MM-DD>-<kebab-slug>`.

**Wait for the user's confirmation or correction.** Don't proceed until OK.

## 5. Write the source (immutable)
Path: `ROOT/sources/transcripts/<YYYY-MM-DD>-<slug>.md` — use `templates/source.md`.
Frontmatter sets `immutable: true`, the classified `type_class`, attendees, date, language. Body is the
full verbatim transcript, grouped by minute or topic, speaker names preserved, timestamps kept. End with a
"Key citations" table mapping `timestamp → quote → target`.

## 6. Write the asset entity
Path: `ROOT/assets/transcripts/<YYYY-MM-DD>-<slug>.md` — use `templates/asset.md`.
Body sections: one-paragraph summary, key takeaways, 3-7 notable verbatim lines with timestamps,
backlinks, and a **Spawns:** list (filled as modules run in step 7).

## 7. Run the output modules
For each module id in the classified type's `outputs` **AND** enabled in `outputs.*`:
read `engine/modules/<module>.md` and run it. Modules:

| module id | file | what it produces |
|---|---|---|
| `crm` | `modules/crm.md` | account + contact records via the configured backend (markdown default) |
| `content` | `modules/content-posts.md` | a post/brief in `ROOT/content/_briefs/` (anonymized if required) |
| `teaching-lessons` | `modules/teaching-lessons.md` | reusable lessons in `ROOT/lessons/` |
| `decisions` | `modules/decisions.md` | a decision log in `ROOT/decisions/` |
| `action-items` | `modules/action-items.md` | a task list (owners + due dates) |
| `call-feedback` | `modules/call-feedback.md` | scorecard + upsell map for the call |

Record every file a module writes in the asset's **Spawns:** section.

## 8. Update index + log
- `ROOT/index.md` — add the asset under a Transcripts section; bump totals.
- `ROOT/log.md` — append:

```
## [YYYY-MM-DD HH:MM] ingest | <slug>
- type: <classified type>
- modules: <list that fired>
- crm: <record created/updated OR n/a>
- lessons: <count> · decisions: <count> · briefs: <count> · action-items: <count>
- asset: assets/transcripts/<file>.md
- source: sources/transcripts/<file>.md
```

## 9. Optional git commit
If `git.auto_commit` is true, stage **only the specific files written this run** (never `-A` blindly)
and commit `loop: ingest <slug>`. Otherwise leave changes for the user to commit.

## 10. Closing line — brief backlog (always)
The transcript→content flywheel only turns if pending briefs stay visible. End every ingest with:
1. Scan `ROOT/content/_briefs/*.md` for `status: brief-only | ready`.
2. Report: **"Brief backlog: N unrun · oldest <date>."**
3. If the oldest unrun brief is >21 days old, add: *"<slug> is N weeks old — run, refresh, or kill?"*

---

## Failure modes
- **Transcript too long for one read** → chunk, read all, then classify.
- **Type drift** → if nothing fits, surface it; don't force-fit.
- **Unclear speakers** → prefix `[Speaker A]/[Speaker B]` and ask before writing the source.
- **CRM facts unverifiable** → never fill factual fields (employee count, website, title) from transcript
  guesses; leave blank. A blank field is recoverable; a wrong field corrupts the CRM. (See `modules/crm.md`.)
- **Anonymization conflict** → if naming someone is plausibly fine, ask before writing their name in a brief.
  Default to anonymous.
