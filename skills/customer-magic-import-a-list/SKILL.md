---
name: customer-magic-import-a-list
description: Bring a list of people or deals onto the user's Customer Magic board in one go — a spreadsheet, a CRM export, scanned badges from an event, a photo of business cards, or names pasted into the chat. Use this skill when the user says "import my contacts", "add this list to Customer Magic", "put these leads on my board", "here is my export", or attaches a file of contacts while Customer Magic is connected. It checks the list against what is already on the board before writing anything, so nothing is added twice and nobody who was already dropped comes back as new work.
---

# Bring a list in

Done means: every person on the list is on the board exactly once, the user was
told the numbers before anything was written, and the deals they will work this
week each have a next step with a date.

It is written for the assistant. The person only has to hand over the list and
answer three or four questions.

---

## Rules

- **Show before you write.** Every import runs first with `dryRun: true`. Tell
  the person what would happen, then wait for a yes.
- **Never invent a field.** An empty cell stays empty. A status you do not
  recognise goes into the notes as text, never into a stage you guessed.
- **Nothing the person typed is thrown away.** A column you cannot place goes
  into that card's notes as `Heading: value`.
- **You contact nobody.** This playbook puts people on a board. It sends nothing.

---

## Step 1 · Confirm where you are writing

Call `whoami` and say the workspace name out loud: *"I'm connected to Acme
Sales."* If the person belongs to several, `list_organizations` and ask which
one. A list imported into the wrong workspace is tedious to undo.

## Step 2 · Get the list and read it back

Take whatever they have: a file, an export, a pasted table, a photo. Then say
what you see before doing anything with it:

- how many rows
- which columns there are, and where each will go
- which columns you cannot place (these go into notes)

| What the list has | Where it goes |
| -- | -- |
| A person's name | `personName` |
| Their company | `company` |
| What the deal is about | `title` (if missing, leave it out — the board builds one from the name and company) |
| Email, LinkedIn, website | `links` |
| Free text, comments, how you met | `notes` |
| Why it matters now | `whyNow` |
| The record's id in another system | `externalId`, written as `system:id` — for example `hubspot:4412` |
| A follow-up and its date | `nextStep` with a `title` and an ISO `dueAt` |

If the list is a plain CSV file, you can pass its text as `csv` instead of
building `rows`. Common headings in English and German are recognised, and
unplaced columns are moved into notes for you and returned as `unmappedColumns`.

## Step 3 · Agree the tag

One tag for the whole batch says where these people came from. Call
`list_campaigns` first and **reuse a tag that exists** rather than adding a
second spelling of it. If this is a new source, agree a short name with the
person ("Expo 2026", "Old CRM") and pass it as `tags` on the import.

## Step 4 · Dry run

Call `import_opportunities` with `dryRun: true`. At most 200 rows per call, so
split a longer list into batches of 200.

Tell the person, in one short message:

- **new** — how many would be added
- **already there** — how many are on the board (dropped ones included) and
  will be left alone
- **problems** — any row that cannot be read, with its row number and why
- any columns that went into notes

A dry run costs nothing. Ask: *"Shall I import these?"*

## Step 5 · Import

On a yes, run the same call without `dryRun`. Keep `onDuplicate` at its default
(`skip`) unless the person asked you to refresh existing cards, in which case
use `update` — it only overwrites the fields the row carries, and only on their
own cards.

Report what was written: created, skipped, and anything that failed.

## Step 6 · Give the ones that matter a next step

An imported deal with no next step is a name on a list. Do **not** give sixty
people the same made-up follow-up.

Ask which of these they will actually contact this week. For each of those,
propose the smallest physical move and a date, and set it with `set_next_action`
once they agree. For the rest, ask once whether to leave them or park them until
a date with `snooze_opportunity`.

If the list shows that somebody was already written to or met, record it with
`record_contact` and pass `occurredAt` with the real date. Otherwise the board
counts last month's mail as today's work.

---

## Say this when you are done

Three lines: how many were added, how many were already there, how many now
have a next step. Then the link to their board.

## When something goes wrong

- **`too_many_rows`** — send 200 at a time.
- **`invalid_csv`** — the message names the row and the field. Fix that row or
  leave it out and say so.
- **A match on a colleague's card** — it is skipped and reported. Tell the
  person whose deal it is; do not create a second card for the same company
  without asking.
- **The whole list is "already there"** — it was imported before. Say so and
  stop.
