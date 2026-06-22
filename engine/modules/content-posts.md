# Module: Content Posts

Turns anything publishable in a transcript into a content brief (and optional draft) for the user's channels.

**Fires when:** the classified type's `outputs` includes `content` AND `outputs.content_posts.enabled` is true.

`ROOT` = `storage.root`.

## 1. Find the publishable angle
Scan for: a story with a clear arc, a counterintuitive insight, a result/number, a strong quote, a
useful how-to. If nothing clears the bar, write **no** brief and say so — don't manufacture filler.

## 2. Apply the voice
Read `outputs.content_posts.voice_file` (default `voices/house.md`) and write in that tone.

## 3. Anonymize if required
If the classified type is in `anonymization.types_requiring_anon` (or `anonymization.default` is true):
strip company names, identifying industry detail, and anything that fingerprints the client. The person
stays mysterious. Keep the *insight*, lose the *identity*.

## 4. Write the brief
Path: `ROOT/content/_briefs/<YYYY-MM-DD>-<slug>.md`. Frontmatter:

```yaml
---
type: brief
status: brief-only        # brief-only | ready | shipped
channels: [<from config: outputs.content_posts.channels>]
source_transcript: <transcript slug>
anonymized: <bool>
created: YYYY-MM-DD
---
```

Body: the hook, the core idea, the supporting points (with the verbatim quote/number if any), and a
suggested structure per target channel. If the user asked for a full draft, draft it under a `## Draft`
heading; otherwise leave it brief-only for a later pass.

## 5. Backlink
Add the brief path to the asset's **Spawns:** section.
