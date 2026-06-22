# The Loop 🔄

**Conversation Intelligence Layer** — paste any meeting transcript, get back the assets your business
actually uses: CRM records, content posts, teaching lessons, decisions, and action items. Automatically
classified and routed based on a config *you* generate in a 2-minute interview.

Runs entirely inside [Claude Code](https://www.anthropic.com/claude-code) on your own machine. No server,
no SaaS, your data stays local. Free and open (MIT).

> Built by [Yuval Keshtcher](https://conversation-intelligence-layer.vercel.app/deck.html). Treat your
> meetings as input, not output.

---

## How it works

```
transcript ──▶ classify (your call types) ──▶ route to your outputs
                                              ├─ CRM record
                                              ├─ content brief / post
                                              ├─ teaching lesson
                                              ├─ decision log
                                              ├─ action items
                                              └─ call scorecard
```

You define your own meeting types and what each one should produce. The engine reads your
`loop.config.yaml` and does the rest.

## Setup (5 minutes)

### 1. Install Claude Code
Follow https://www.anthropic.com/claude-code. You'll need a terminal.

### 2. Get a transcript source — Spinach (recommended)
The Loop needs transcripts as input. We recommend **[Spinach AI](https://spinach.ai)** — it records your
meetings and produces clean bilingual (Hebrew/English) transcripts with an API/MCP you can wire in.

> 💡 **When you sign up for Spinach, say "Yuval sent you" to get the deal.**

(Any transcript works — Fathom, Fireflies, Otter, a Google Doc, or plain text you paste in.)

### 3. Clone this repo and open it in Claude Code
```bash
git clone https://github.com/Yuvalkesh/the-loop.git
cd the-loop
claude
```

### 4. Run onboarding
In Claude Code:
```
/loop-onboard
```
It interviews you about your business, your meeting types, and what you want out of each call, then writes
your `loop.config.yaml` and sets up your data folders. (See `loop.config.example.yaml` for a worked example.)

### 5. Ingest a transcript
Paste a transcript or point to a file, then:
```
/loop-ingest
```
or just say *"ingest this call."* The engine classifies it, pauses to confirm, and routes it to your
configured outputs.

## CRM backends

CRM is optional and defaults to **markdown** — records become local files under `loop/crm/`, zero setup,
fully offline. During onboarding you can instead connect **Airtable**, **Notion**, **Google Sheets**, or a
**custom** tool.

🔐 **Secrets:** never put API keys in `loop.config.yaml`. Store the *name* of an environment variable
(e.g. `pat_env: AIRTABLE_PAT`) and export it in your shell: `export AIRTABLE_PAT=...`

## What's in here

| Path | What it is |
|---|---|
| `.claude/skills/loop-onboard/` | the setup interview skill |
| `.claude/skills/loop-ingest/` | the transcript engine skill |
| `engine/onboarding-workflow.md` | the interview script |
| `engine/ingest-workflow.md` | the config-driven ingest pipeline |
| `engine/modules/` | one file per output (crm, content, lessons, decisions, action-items, call-feedback) |
| `templates/` | neutral entity templates the engine fills |
| `loop.config.example.yaml` | worked example config |
| `loop/` | your data lives here (git-ignored, created at onboarding) |

Your `loop.config.yaml` and everything under `loop/` are git-ignored, so a fresh clone always starts clean.

## License
MIT — free to use, modify, and share.
