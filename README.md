# The Loop 🔄

**Conversation Intelligence Layer** — paste any meeting transcript, get back the assets your business
actually uses: CRM records, content posts, teaching lessons, decisions, and action items. Automatically
classified and routed based on a config *you* generate in a 2-minute interview.

Runs entirely inside [Claude Code](https://www.anthropic.com/claude-code) on your own machine. No server,
no SaaS, your data stays local. Free and open (MIT).

> Built by [Yuval Keshtcher](https://conversation-intelligence-layer.vercel.app/deck.html). Treat your
> meetings as **input**, not output.

---

## The idea

Most of your company's knowledge is spoken — in sales calls, client sessions, team syncs, interviews — and
most of it evaporates the moment the call ends. The Loop treats every conversation as a **raw material**:
you feed it the transcript, and it produces the structured assets you'd otherwise make by hand.

It does this with **two Claude Code skills** and a config file you own:

| | |
|---|---|
| 🛠️ **`/loop-onboard`** | A one-time interview. It learns your business, your meeting types, and what each one should produce — then writes your `loop.config.yaml`. |
| 🔄 **`/loop-ingest`** | The engine. Reads your config, classifies any transcript you paste, and routes it to your chosen outputs. |

```
transcript ──▶ /loop-ingest ──▶ classify (your call types) ──▶ route to your outputs
                                                               ├─ CRM record
                                                               ├─ content brief / post
                                                               ├─ teaching lesson
                                                               ├─ decision log
                                                               ├─ action items
                                                               └─ call scorecard
```

---

## How the skills work

### 🛠️ `/loop-onboard` — runs once, configures everything

When you first clone the repo there's no config yet, so the engine doesn't know *your* business. Onboarding
fixes that with a short interview. It asks:

1. **Your business** — name, what you do, working language (English / Hebrew / mixed).
2. **Your meeting types** — *"What kinds of conversations do you have?"* You pick and rename from a starter
   menu (sales call, client delivery, internal sync, interview, podcast, 1:1…). These become **your**
   taxonomy — the engine classifies every future transcript against this list, not a generic one.
3. **What each type should produce** — for each meeting type: *"When this call ends, what do you want out of
   it?"* You map it to outputs (CRM update, content post, teaching lesson, decision log, action items, call
   scorecard). A sales call might produce CRM + content + action items; an internal sync might just produce
   decisions + action items.
4. **Your CRM** — markdown by default (zero setup), or connect Airtable / Notion / Google Sheets / a custom
   tool. (See [CRM backends](#crm-backends).)
5. **Content voice** — if any meeting type produces content, it asks your channels and tone, and seeds a
   starter `loop/voices/house.md` so posts sound like you.
6. **Confidentiality** — which meeting types involve clients whose names must be stripped from anything
   published.

It then shows you a preview, waits for your OK, and **writes `loop.config.yaml` + creates your `loop/` data
folders**. You only run this once (re-run it any time to reconfigure).

### 🔄 `/loop-ingest` — runs on every transcript

Paste a transcript (or point at a file) and run it. The engine:

1. **Reads the whole transcript** — never just the first chunk; calls pivot halfway.
2. **Classifies it** against *your* `call_types`. If it's ambiguous, it asks rather than guessing.
3. **Shows you a 3-line takeaway and pauses** — what the call was, the type it picked, and which outputs will
   fire. Nothing is written until you confirm.
4. **Saves an immutable source** copy of the transcript, plus a short **asset** summary (key takeaways +
   notable quotes).
5. **Runs your output modules** for that meeting type — each one writes its files:
   - **CRM** → account + contact records (people, company, stage, next step), researched and verified first.
   - **Content** → a ready-to-write brief (or full draft) in your voice, anonymized if the type requires it.
   - **Teaching lessons** → reusable insights worth keeping for your team or audience.
   - **Decisions** → a log of what was actually decided, with owner and deadline.
   - **Action items** → a checklist of tasks with owners and due dates.
   - **Call scorecard** → a private review of how *you* ran the call, plus an upsell map (sales-type calls).
6. **Logs the run** and ends with a **brief backlog** reminder so publishable ideas don't pile up unused.

Everything lands as plain markdown under `loop/` (or your configured CRM), so it's yours, greppable, and
git-friendly.

---

## Setup

### 1. Install Claude Code
Follow https://www.anthropic.com/claude-code. You'll need a terminal. (Claude Code is what runs the two
skills — The Loop is not a separate app.)

### 2. Get a transcript source — Spinach (recommended)
The Loop needs transcripts as input. We recommend **[Spinach AI](https://spinach.ai)** — it joins your
meetings, records them, and produces clean **bilingual (Hebrew/English)** transcripts with an API/MCP you can
wire in later.

> 💡 **The deal:** Spinach offers a **14-day free trial**. During Spinach's own onboarding, when it asks
> *how you heard about it*, answer **"Yuval"** — and they'll send you a discount.
>
> *(I'm not affiliated with Spinach and earn nothing from this — they simply offered this deal to people who
> come from me.)*

Any transcript works, though — Fathom, Fireflies, Otter, a Google Doc, or plain text you paste in. Spinach is
just the smoothest capture front-end, especially for Hebrew.

### 3. Clone this repo and open it in Claude Code
```bash
git clone https://github.com/Yuvalkesh/the-loop.git
cd the-loop
claude
```

### 4. Run onboarding (once)
In Claude Code:
```
/loop-onboard
```
Answer the interview. It writes your `loop.config.yaml` and sets up your `loop/` folders. (Peek at
`loop.config.example.yaml` for a worked example first if you like.)

### 5. Ingest a transcript (every time)
Paste a transcript or point to a file, then:
```
/loop-ingest
```
or just say *"ingest this call."* Confirm the takeaway when it pauses, and it routes everything to your
configured outputs.

---

## CRM backends

CRM is optional and defaults to **markdown** — records become local files under `loop/crm/`, zero setup,
fully offline. During onboarding you can instead connect:

| backend | needs | how |
|---|---|---|
| **markdown** *(default)* | nothing | account + contact files under `loop/crm/`, cross-linked |
| **Airtable** | base id, table id, a PAT | records pushed to your table via the Airtable MCP/API |
| **Notion** | database id, integration token | pages in your CRM database via the Notion MCP/API |
| **Google Sheets** | sheet id | one row per account via a connected Sheets MCP |
| **custom** | — | a stub you wire to your own tool |

🔐 **Secrets:** never put API keys in `loop.config.yaml`. Store the *name* of an environment variable
(e.g. `pat_env: AIRTABLE_PAT`) and export it in your shell: `export AIRTABLE_PAT=...`

---

## What's in here

| Path | What it is |
|---|---|
| `.claude/skills/loop-onboard/` | the setup-interview skill |
| `.claude/skills/loop-ingest/` | the transcript-engine skill |
| `engine/onboarding-workflow.md` | the interview script the onboard skill follows |
| `engine/ingest-workflow.md` | the config-driven ingest pipeline |
| `engine/modules/` | one file per output (crm, content, lessons, decisions, action-items, call-feedback) |
| `templates/` | neutral entity templates the engine fills in |
| `loop.config.example.yaml` | worked example config |
| `loop/` | your data lives here (git-ignored, created at onboarding) |

Your `loop.config.yaml` and everything under `loop/` are git-ignored, so a fresh clone always starts clean
and your data never gets committed by accident.

---

## FAQ

**Do I need to be technical?** No. You talk to Claude Code in plain language; the skills do the file work.

**Where does my data go?** Nowhere by default — it's local markdown on your machine. Only the CRM backends
you explicitly connect (Airtable/Notion/Sheets) send anything outward.

**Can I change my setup later?** Yes — re-run `/loop-onboard` any time, or edit `loop.config.yaml` directly.

**Does it work in Hebrew?** Yes. Set `language: he` (or `mixed`) in onboarding. Spinach handles bilingual
capture well.

## License
MIT — free to use, modify, and share.
