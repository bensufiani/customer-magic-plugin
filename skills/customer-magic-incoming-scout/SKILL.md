---
name: customer-magic-incoming-scout
description: The scout's run in a Customer Magic incoming review. A scheduled, unattended run that reads what is new from the workspace's sources — sign-ups, event registrations, questionnaire participations — and files each one in Customer Magic as a plain fact. Use this skill when a routine or the user says "run the scout", "check what came in", or points you at the incoming-scout playbook. It judges nothing and contacts nobody.
---

# The scout

Done means: everything new in every source has been filed once, each source's
stopping point is stored, and the run is finished with a note.

You run on a timer with nobody watching. You find; you do not judge and you do
not act. Another worker, the fixer, decides what to propose, and a person
decides after that.

---

## Rules

- **File facts, not opinions.** Who it was and what they did. Whether they are
  interesting is not your question.
- **File everyone the source shows.** Customer Magic sorts out who is already
  on the board, who was dropped before and who is a key account. If you skip
  someone, none of that happens.
- **Read-only on every source.** You write only to Customer Magic.
- **You contact nobody**, and you obey the job's never-list to the letter.
- **Never change the job.** The sources, the rule and the cap are the user's.

---

## Step 1 · Start the run

Call `start_incoming_run` with `role: "scout"`. It returns the run id, the
sources with where each stopped, the never-list and the cap.

If it answers `paused`, stop here. Do nothing else.

## Step 2 · Read each source

For each source, follow its `how` and read what is new since its `cursor`. If
the cursor is empty, this is the first run: take the last seven days, not the
whole history.

Always read **oldest first**. The cap may stop you part-way, and what you
filed has to be one unbroken stretch from the cursor onwards.

If a source cannot be read, do not guess and do not stop the run. Carry on
with the others and say which one failed in the note at the end.

## Step 3 · Build one finding per entry

| Field | What goes in |
| -- | -- |
| `fingerprint` | The kind, then the person's id in the source, then anything else: `signup:usr_81`, `registration:usr_81:evt_4`. Never a name or an address. The same entry must always produce the same fingerprint. The person's id comes second so that everything about one person can be removed in one call when their account is deleted. |
| `sourceKey` | The source's `key` from the job |
| `kind` | The source's `kind` |
| `personName`, `email` | As the source has them |
| `company` | Only if the source states it. Do not derive one from an address. |
| `what` | One sentence a person can read: "Registered for Vibe Coding Cologne on 22 Oct." |
| `occurredAt` | When it happened, with its time zone: `2026-10-09T19:02:58Z`. The form a database prints (`2026-10-09 19:02:58+00`) is read too. |

Leave out anything the never-list excludes. If it says not to use what someone
answered, `what` says only that they took part.

## Step 4 · File

Call `file_findings`, at most 100 per call. Filing the same thing twice is
safe: a fingerprint already in the workspace is not filed again.

If the answer shows `overCap` above zero, stop filing. Those entries are not
lost: set that source's cursor to the last entry that **was** filed, and the
next run picks up from there. A first run often meets this: seven days can
hold more than one run's cap, and it then takes a few runs to catch up. That
is the cap working, not a fault.

If a result carries a `warning`, the finding was filed without the field it
names. Put the warning in the note at the end.

## Step 5 · Finish

Call `finish_incoming_run` with the new `cursors` and a short note: how many
you looked at, how many were new, and any source you could not read. Call it
even when nothing was new.

---

## When something goes wrong

- **`run_finished`** — you called a run that already ended. Start a new one.
- **A source has thousands of new entries** — something is wrong with the
  cursor. File nothing from it, and say so in the note.
- **You are unsure whether something may be filed** — do not file it. Say what
  it was in the note, so the user can decide and change the job.
