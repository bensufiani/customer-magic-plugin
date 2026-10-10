---
name: customer-magic-daily-run
description: Work through what is due today on the user's Customer Magic board, one deal at a time, and leave every one of them with a new dated next step. Use this skill when the user says "what should I do today", "run my morning", "work my list", "what's overdue", "let's do my follow-ups", or opens a session with Customer Magic connected and no other task. It ends with nothing overdue: each due step is either done with proof, moved with a reason, or dropped.
---

# Work today's list

Done means: nothing is overdue, every deal you touched has a new next step with
a date, and the person knows the three things waiting for them tomorrow.

The board already ranks the work. Your job is to get through it with the person
and keep the record honest while you do.

---

## Rules

- **Top three first.** Do not read out forty items. Start with the three that
  matter most and ask before going further.
- **Done needs proof.** `complete_action` only with a reference — a sent-message
  id, a calendar event id, a link. No reference: use `flag_action` and let the
  person confirm with one tap.
- **A deal never leaves your hands without a next step.** Finish one, name the
  next in the same turn.
- **An overdue step is settled, not carried.** Done, moved with a reason, or
  dropped.

---

## Step 1 · Catch up

Call `list_recent_activity` with `since` set to the end of the last session if
you know it. Say in one or two lines what happened while you were away: steps
confirmed from the morning mail, deals a colleague handed over, anything won.

## Step 2 · Read what is due

Call `list_open_actions`. Split it into *overdue*, *due today* and *later*, and
show the first three with the person's name, the step and how late it is.

Also name the gap: any active deal with **no** open step. Those are the ones
that go quiet without anyone deciding to let them.

## Step 3 · Take them one at a time

For each step, ask what happened or do it together.

**It is done.**
Record the contact with `record_contact` (`sent`, `reply`, `meeting_booked` or
`meeting_held`), with a note saying where it lives. Then `complete_action` with
the reference. Then `set_next_action` — the smallest physical move, with a date.

**They want to do it now.**
If they ask for a draft, call `get_workspace_profile` first so it sounds like
them and describes what they actually sell. They send it, or you do if your
harness can and they said so for this message. Then record it as above.

**You think it is done but cannot point to it.**
`flag_action` with what you saw. The step stays open and the board shows
"probably done" until they confirm.

**Not today.**
`redate_action` with the new date and the reason, in their words. If the whole
deal should wait, `snooze_opportunity` until a real date.

**Not ever.**
`cancel_action` if only the step is wrong, then name the right one.
`archive_opportunity` with the reason if the deal is dead.

## Step 4 · Something new came up

A new name in a reply, a referral, an idea: `create_opportunity` now, with a
next step, while it is still in their head.

## Step 5 · Close the session

Three lines:

1. what got done
2. what was moved, and to when
3. the three things due next

---

## When something goes wrong

- **You marked something done that was not** — `void_completion` with the
  reason. The step reopens and the record keeps both entries.
- **The list is too long to face** — that is a weekly review, not a morning.
  Offer the *Weekly review* playbook and do today's top three anyway.
- **A step is on a colleague's deal** — you can read it, not change it. Tell
  the person whose it is, or take the deal over with `assign_opportunity` if
  they say so.
