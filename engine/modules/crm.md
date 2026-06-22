# Module: CRM

Turns a sales/client/relationship transcript into account + contact records. Backend-agnostic:
the default is **markdown** (local files, zero setup); Airtable / Notion / Sheets / custom are adapters
selected by `outputs.crm.backend`.

**Fires when:** the classified type's `outputs` includes `crm` AND `outputs.crm.enabled` is true.
Typically sales / client / relationship calls — not internal syncs, podcasts, or interviews.

`ROOT` = `storage.root`.

## 1. Pre-flight research (mandatory)
**Never fill factual fields from transcript guesses.** Before writing/updating any record:
- **Web-search the company** to verify website, industry, rough size, recent news — only if a web tool is available.
- **Email search** for prior threads with anyone at the company — only if a mail MCP is connected.
- Compile a short verified-facts block. Anything you can't verify, **leave blank**. A blank field is
  recoverable; a wrong field corrupts the CRM.

If no web/mail tools are connected, skip enrichment and proceed with only what the transcript states,
clearly marking unverified fields blank.

## 2. Resolve the account
Search existing records (by company, and by abbreviations / other-language spellings) before creating a
new one. A duplicate is harder to fix than an extra search.

## 3. Write per backend

### backend: markdown  (DEFAULT)
- **Account:** `ROOT/crm/accounts/<company-slug>.md` from `templates/account.md`. If it exists: append the
  transcript slug to `related_transcripts`, bump `last_updated`, move `stage`/`next_step` if the call moved
  them, and append a dated bullet to the Pipeline section.
- **Contacts:** one file per named person on the other side at `ROOT/crm/contacts/<firstname>-<lastname>.md`
  from `templates/contact.md`. Fill email/phone/LinkedIn only from verified research; else blank.
- **Bidirectional links are mandatory:** the account's **Key contacts** section `[[links]]` each contact, and
  each contact's `related_accounts` points back.

### backend: airtable
Use the Airtable MCP (or API). Creds come from `outputs.crm.airtable.pat_env` (an env-var **name**) and the
configured `base_id` / `table_id`. Search by Company first; update `Last Activity`, bump `Meetings Count`,
move `Stage`, append a dated `Notes` line. Mirror the created record id back into the markdown account file's
`crm_record_id` so the two surfaces know about each other.

### backend: notion
Use the Notion MCP/API with `outputs.crm.notion.{database_id, token_env}`. Find the company page or create
one; update Stage, Last Activity, and append a note block. Mirror the page id into the markdown account file.

### backend: sheets
Use a connected Sheets MCP with `outputs.crm.sheets.sheet_id`. Append/update one row per account
(Company, Primary Contact, Stage, Last Activity, Next Step, Notes).

### backend: custom
Leave a clearly-marked stub describing what the user should wire (which tool, which fields). Do not invent
an integration.

## 4. Pause before NEW records / stage moves
Before creating a **new** account/contact or moving a **stage**, surface to the user: the verified-facts
block + the proposed record(s), field by field. **Wait for OK.** Touches to existing records (last activity,
meeting count, appended notes) may proceed if the step-4 takeaway was already approved.

## 5. Booked follow-up
If the call ends with a scheduled follow-up and a calendar MCP is connected, look up the actual event to
capture date/time + event id (people misremember; invites move). Record it on the account
(`next_meeting`) and in the backend. If no calendar tool, record the agreed time verbatim and flag it
as not-yet-verified.
