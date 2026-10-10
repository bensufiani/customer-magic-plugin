---
name: customer-magic-weekly-review
description: Go through the user's whole Customer Magic board once a week and make a decision on everything that has slipped — overdue steps, deals with no next step, conversations gone quiet, events not followed up — so the board is true again and Monday starts with a short list. Use this skill when the user says "weekly review", "clean up my pipeline", "my board is a mess", "everything is overdue", "what did I miss", "prepare my week", or when their list of overdue steps has grown too long to work in a morning.
---

# Weekly review

Done means: nothing is overdue, every active deal has a next step with a date,
everything that is not being worked is parked or dropped with a reason, and the
person has three things for Monday.

A board goes stale one skipped day at a time. This is the half hour that brings
it back, and the rule that makes it work is simple: every slipped item gets a
decision, not a new date picked to make it go away.

---

## Rules

- **Decide in batches.** Show a table with your proposed call per deal and let
  the person correct it. Never forty questions in a row.
- **Four calls, no fifth.** Do it this week, move it with a reason, park it
  until a date, drop it with a reason.
- **Never move a date without a reason.** A week later, a slipped date and a
  deliberate one look the same.
- **Nothing is deleted.** Dropped deals stay findable, with why.

---

## Step 1 · What happened this week

Call `list_recent_activity` for the last seven days. Say it in a few lines:
contacted, replied, met, won, dropped. This is the only good news in the
review, so lead with it.

## Step 2 · Everything overdue

Call `list_open_actions` and take the overdue ones, oldest first. Show them as
a table: who, what the step was, how late, and your proposed call.

| Call | What you do |
| -- | -- |
| Still doing it | `redate_action` to a day this coming week, with the reason it slipped |
| It happened | `record_contact`, then `complete_action` with the reference — or `flag_action` if there is none — then `set_next_action` |
| Wrong step | `cancel_action` with the reason, then `set_next_action` |
| Not now | `snooze_opportunity` until a real date, with the reason |
| Dead | `archive_opportunity` with the reason |

Apply the whole table once the person has corrected it.

## Step 3 · Deals with no next step

Call `list_opportunities`. Any active deal without an open step is drifting.
For each: name a step with a date, park it, or drop it. Offer the same table.

Look at the `stage` too. A deal still reading `untouched` weeks after it was
added was either never contacted, or it was and nobody recorded it. Ask which,
and fix the record with `record_contact` and the real date.

## Step 4 · Events and campaigns

Call `list_campaigns`. For every open one with `needsFollowUp` above zero, name
the deals and settle them. Ask whether any finished event should be closed
(`save_campaign`, `state: "closed"`).

## Step 5 · Money

Ask once: did anything get paid or invoiced this week that is not on the board?
Record it with `record_revenue` and the real date.

## Step 6 · If they lead a team

Call `list_opportunities` with `owner: "all"` and `list_members`. Give a short
read per person: open deals, overdue steps, deals with no next step. You can
read colleagues' deals but not change them — the point is to know who to talk
to, not to tidy up for them.

## Step 7 · Monday

End with three things, each a deal, a step and a day. If the person names more
than three, ask which three come first.

---

## When something goes wrong

- **Over fifty overdue** — do the oldest twenty now and say how many are left.
  A finished half is worth more than an abandoned whole.
- **The person wants to move everything to next week** — say plainly that this
  is the same list seven days later, and ask which five they will really do.
  Park the rest with honest dates.
- **A deal belongs to someone who left** — see the *Hand over deals* playbook.
