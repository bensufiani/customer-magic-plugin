---
name: customer-magic-crm-sync
description: Keep the user's Customer Magic board and another system in step — a CRM such as HubSpot, Attio, Pipedrive or Salesforce, or a spreadsheet — in one direction or both. Use this skill when the user says "sync my CRM", "keep HubSpot and Customer Magic in step", "push my board to the sheet", "update the CRM with what I did", or asks why the two disagree. You need a working connection to both systems. Customer Magic connects to nothing itself, so you are the one who carries changes across.
---

# Keep a CRM in step

Done means: every deal the person wants in both places exists in both, each
knows the other's id, and what changed on one side since the last run has
reached the other.

Customer Magic has no connection to any CRM, and that is deliberate. You hold
both connections, so you carry the changes. Three tools exist for exactly this:
`import_opportunities`, `export_opportunities` and `list_changes`.

---

## Rules

- **Agree the map before the first write.** Which records, which fields, which
  direction, and which side wins when both changed.
- **Never delete on either side.** A deal dropped on the board is a status
  change in the CRM at most, and only if the person asked for that.
- **Never pick a winner silently.** When the same field changed on both sides,
  show both values and ask.
- **Every paired deal carries an `externalId`**, written as `system:id`
  (`attio:rec_8f2`, `sheet:row-14`). It is the only thing that makes a second
  run safe.

---

## First run

### 1 · Prove both connections

Read one real record from the other system and one card from the board
(`list_opportunities`). If either fails, stop and fix that first.

### 2 · Agree the map

Ask, and write the answers down:

| Question | A sensible default |
| -- | -- |
| Which records? | One pipeline, list or filter — not the whole CRM |
| Which direction? | Both |
| Who owns company and contact details? | The CRM |
| Who owns the next step, the notes and the history? | The board |
| What happens to a deal dropped on the board? | Nothing in the CRM until the person says |

Keep this map, and the cursor from step 5, wherever your harness keeps things
between sessions. **If it keeps nothing, say so** and give the person a short
note to paste in next time. A sync that forgets its place starts from the
beginning every run.

### 3 · CRM → board

Build rows from the CRM records, each with its `externalId`, and call
`import_opportunities` with `dryRun: true`. Tell the person how many are new
and how many are already on the board, then import on a yes.

### 4 · Board → CRM

Call `export_opportunities` and take the cards that have no `externalId`. Ask
which of them belong in the CRM. Create those there, then write each new CRM id
back with `update_opportunity` (`externalId`).

### 5 · Store the starting point

Call `list_changes` with `madeBy: "others"` and keep calling while `more` is
true. Push nothing — everything is already in step. Store the last `cursor`.

---

## Every run after that

### 1 · Board → CRM

Call `list_changes` with `madeBy: "others"` and `since` set to your stored
cursor. `others` leaves out what connected assistants wrote, so you never read
your own writes back.

For each change, find the CRM record by `externalId` and apply what the map
allows:

- `edited` — the `changes` object gives each field's `from` and `to`
- contact recorded, a note added, a step completed — an activity or note in the CRM
- `created` with no `externalId` — ask whether it belongs in the CRM
- `assigned` — a new owner, if the CRM tracks one

Keep calling while `more` is true.

### 2 · CRM → board

Fetch the CRM records changed since your last run. Send them through
`import_opportunities` with `onDuplicate: "update"`. It writes only the fields
each row carries. Dry-run first whenever it is more than a handful.

### 3 · Store the new cursor and report

Three numbers: pushed to the CRM, pulled onto the board, left for the person to
decide. Then list the ones that need a decision, each with both values.

---

## When something goes wrong

- **A change names a card with no `externalId`** — it was never paired. Ask
  before creating anything in the CRM.
- **The CRM record is gone** — say so. Do not drop the card on your own.
- **A match on a colleague's card** — imports skip it. Their own assistant, or
  an admin, has to update it.
- **You lost the cursor** — run "First run" again. Imports are safe to repeat,
  so nothing is duplicated; it only takes longer.

## If your harness can run on a schedule

Offer it once: a sync every morning before the person starts. If it cannot,
the sentence "sync my CRM" does the same job whenever they say it.
