---
name: customer-magic-after-a-conversation
description: Record a sales conversation on the user's Customer Magic board straight after it happened — a call, a meeting, a demo, a reply that came in, a chat at an event — so the deal shows what was said, how far it has moved and what happens next. Use this skill when the user says "I just spoke to…", "log this call", "she replied", "we had the demo", "note that down", "update the deal", or pastes meeting notes or a transcript while Customer Magic is connected.
---

# After a conversation

Done means: the deal shows that the conversation happened and when, what was
said is in its history, and there is one next step with a date.

It takes a minute when it is done straight away, and it is the minute that
decides whether the next conversation starts where this one ended.

---

## Rules

- **Their words, not your summary of them.** Keep names, numbers, dates and
  objections as they were said.
- **What happened and what was noted are two different records.** Contact moves
  the deal along the board. A note does not.
- **Only real money is revenue.** An amount that was quoted or hoped for is a
  note.
- **One next step, with a date, before you stop.**

---

## Step 1 · Find the deal

Call `list_opportunities` and match on the person and the company.

- **One match** — say which one you are updating.
- **Several** — ask which.
- **None** — `create_opportunity` with the person, the company and how the
  conversation came about.
- **It is a colleague's deal** — you can read it but not change it. Say whose
  it is and ask whether to take it over with `assign_opportunity`.

## Step 2 · Record that it happened

Call `record_contact` with what it was:

| What happened | `what` |
| -- | -- |
| They wrote or called back | `reply` |
| A call or demo is now in the calendar | `meeting_booked` |
| The call, meeting or demo took place | `meeting_held` |
| The person wrote or called them | `sent` |

Put where it lives in the note — the thread, the calendar event, the recording.
If it did not happen just now, pass `occurredAt` with the real time.

## Step 3 · Write down what was said

Call `add_note` with what the next person reading this deal needs:

- what they want, in their words
- what they objected to or are unsure about
- who else is involved in the decision
- any number, date or deadline that was mentioned
- what was promised, by whom

If the person pasted a transcript or long notes, keep those five things and
drop the small talk. Do not add conclusions they did not state.

## Step 4 · Settle the step that was open

Call `list_open_actions` and look at this deal's step.

- **This conversation was that step** — `complete_action` with the reference
  (the calendar event, the message). No reference: `flag_action`.
- **It was something else and still matters** — leave it, or `redate_action`
  with the reason.
- **It no longer makes sense** — the next step replaces it.

## Step 5 · Name the next step

Call `set_next_action` with the smallest physical move and a date. "Send the
offer" is a goal. "Copy last month's offer, change the seats to 12, send it" is
a step.

If the ball is with them, the step is a dated nudge: "Ask Anna whether legal
has looked at it" on the day they said.

## Step 6 · Anything else that changed

- **Money arrived or was invoiced** — `record_revenue` with the amount and the
  date. Never a quote or a forecast.
- **Not now** — `snooze_opportunity` until the date they named, with the reason.
- **It is dead** — `archive_opportunity` with the reason in their words.

---

## Say this when you are done

Two lines: what you recorded, and the next step with its date. Nothing else.

## When something goes wrong

- **You are not sure it is the right deal** — ask. A note on the wrong card
  misleads two deals at once.
- **Several conversations to log** — do them one at a time in the order they
  happened, each with its own `occurredAt`.
