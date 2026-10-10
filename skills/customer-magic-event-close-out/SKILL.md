---
name: customer-magic-event-close-out
description: Turn the contacts from an event — a trade show, a conference, a meetup, a webinar — into worked deals on the user's Customer Magic board, and close the event out properly: everyone captured, everyone sorted, every conversation that started given a dated next step, and a short summary at the end. Use this skill when the user says "I'm back from the expo", "here are the badges", "follow up the conference", "close out the event", "what came of the trade show", or attaches a stack of business cards or a lead-scanner export.
---

# Close out an event

Done means: everyone you met is on the board under one tag, each has been
sorted into *this week*, *later* or *no*, nobody you have been in touch with is
left without a next step, and the person has a five-line summary of what the
event produced.

Most event follow-up dies in the first week, with the cards still in a jacket
pocket. This playbook is the week after, in order.

---

## Rules

- **One tag for the whole event**, agreed once and reused. Two spellings of the
  same event are two halves of a group nobody can see whole.
- **Sort everyone. Chase few.** A next step you will not do is worse than an
  honest "later".
- **You send nothing unless the person asks you to**, one message at a time.
- **The event is not closed while someone you contacted has no next step.**

---

## Step 1 · Name the event

Call `list_campaigns`. If the event is there, use its tag exactly as spelled.
If not, agree a short name and describe it with `save_campaign`: one sentence
on what it was, and the first and last day.

## Step 2 · Get everyone onto the board

Take what they have: a scanner export, a spreadsheet, photos of cards, names
from memory. Use `import_opportunities` with the event's tag in `tags`, and
**always dry-run first** — tell them how many are new and how many they already
knew. (The *Bring a list in* playbook covers this step in detail.)

Put what was said at the stand into each card's `notes` while the person still
remembers it. That sentence is what the follow-up will be written from.

## Step 3 · Sort

Call `list_opportunities` with the event's `tag` and go through them with the
person. Do it as one table with your proposed call per row, so they correct
rather than answer sixty questions.

| Call | What you do |
| -- | -- |
| **This week** — a real conversation, worth a follow-up now | `set_next_action` with the smallest physical move and a date |
| **Later** — right person, wrong time | `snooze_opportunity` until a real date, with the reason in their words |
| **No** — wrong fit, a competitor, a student | `archive_opportunity` with the reason |

## Step 4 · Work the follow-ups

As each one goes out, record it with `record_contact` (`sent`), then name what
comes next with `set_next_action` — usually a dated nudge. When a reply comes
back or a call is booked, record that too (`reply`, `meeting_booked`).

A message only mentioned in a note does not move the deal on the board.

## Step 5 · The close-out check

Call `list_campaigns` and read the event's `tally`:

- cards per stage — how far each conversation got
- revenue so far
- **`needsFollowUp`** — people you have been in touch with who have no next step

If `needsFollowUp` is above zero, the event is not finished. List those deals
and settle each one: a next step with a date, parked, or dropped with a reason.

## Step 6 · Summary, then close

Give the person five lines:

1. how many people were captured
2. how many were contacted, replied, met
3. what has been won, in money, if anything
4. what is still open and when each is due
5. how many were parked or dropped, and the most common reason

Ask whether to close the campaign. On a yes, `save_campaign` with
`state: "closed"`. Closing takes it out of the board's filter row and changes
no deal — the open follow-ups stay on the worklist.

---

## When something goes wrong

- **The list is mostly "already there"** — good news: they were known. Add the
  event's tag to those cards with `update_opportunity` so the tally counts
  them. `tags` replaces the whole list, so send the card's existing tags plus
  the new one.
- **Nobody remembers what was said** — say so in the note rather than inventing
  a conversation. "Met at the stand, no notes" is an honest card.
- **It is three weeks later** — still do it. Record what was sent with its real
  date (`occurredAt`), and start at Step 3.
