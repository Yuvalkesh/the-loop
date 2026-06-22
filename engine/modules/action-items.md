# Module: Action Items

Pulls concrete tasks out of a conversation — what, who, by when.

**Fires when:** the classified type's `outputs` includes `action-items` AND `outputs.action_items.enabled`
is true.

`ROOT` = `storage.root`.

## 1. Extract tasks
Capture every commitment to *do something*. For each: the **task**, the **owner** (who agreed to it), and a
**due date** if one was stated (else leave blank — don't invent deadlines). Distinguish "I'll send the deck"
(action item) from "we should think about X someday" (not one).

## 2. Write
Append to the transcript's asset under an `## Action items` section, as a checklist:

```markdown
## Action items
- [ ] <task> — owner: <name> — due: <YYYY-MM-DD or TBD>
```

If `outputs.action_items` names an external task tool (e.g. via a connected MCP), also push each item there;
otherwise the asset checklist is the system of record.

## 3. Surface
List the extracted action items back to the user at the end of the run so nothing is buried.
