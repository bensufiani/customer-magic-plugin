---
name: customer-magic-hand-over
description: Move deals from one person to another inside a Customer Magic workspace so that nothing is lost in the handover — for holiday cover, a colleague leaving, a territory change, or one deal that belongs with someone else. Use this skill when the user says "give this deal to Anna", "I'll take that one over", "cover my deals while I'm away", "Tom is leaving, move his deals", "reassign", "who owns this", or asks to rebalance work across the team.
---

# Hand over deals

Done means: each deal has its new owner, arrives with a next step and a
"where this stands" note, and the person receiving it has a list of what
landed on their board.

A deal handed over without context starts again from zero. The move takes one
call; this playbook is the part before and after it.

---

## Who can move what

| You are | You can |
| -- | -- |
| The deal's owner | Hand it to anyone in the workspace |
| Any member | Take a colleague's deal over for yourself |
| An admin | Move any deal to anyone |

Everyone can read every deal. Only its owner can change it. So a note on a
colleague's deal has to be written **before** it leaves its owner, or **after**
it has arrived with you.

---

## Rules

- **Decide the list first, move second.** Show who gets what before any deal
  moves.
- **A deal travels with its next step and its history.** Make sure it has both.
- **You tell nobody on your own.** Give the person the message for their
  colleague; they send it.
- **Move the deals before anyone is removed from the workspace.** Removing a
  member does not move their deals.

---

## Step 1 · Who is involved

Call `list_members`. Confirm who is handing over and who is receiving, by name.

## Step 2 · What is moving

Call `list_opportunities` with `owner` set to the person handing over (their
user id, or leave it out for the person you are working with). Show the deals
as a table: who, stage, next step, and your proposed new owner.

For holiday cover, ask which deals actually need someone while they are away.
The rest can simply be parked until the return date with `snooze_opportunity`.

## Step 3 · Make each deal readable cold

Before a deal leaves, its current owner's assistant should check two things:

- **A next step with a date** — if missing, `set_next_action`.
- **Where this stands** — one `add_note` covering the last contact, what was
  promised, and anything the new owner must not get wrong.

If you are working for the receiver instead, take the deal over first and then
ask the previous owner for those two things.

## Step 4 · Move

Call `assign_opportunity` for each deal: `to` is the new owner's user id from
`list_members`, or `"me"` to take one over. The next step and the full history
go with it, and the change is recorded on the deal.

## Step 5 · Tell the receiver

Give the person a short message to send their colleague: how many deals, which
ones are due this week, and the one thing to know about each. Do not send it
yourself unless they ask.

## Step 6 · Coming back

After a holiday, list the deals that were moved (`list_changes` shows every
`assigned` entry) and take them back with `assign_opportunity`, `to: "me"`.
Read the notes the cover left before the first follow-up.

---

## When something goes wrong

- **`assign_opportunity` is refused** — you are neither the owner, the
  receiver, nor an admin. Ask the owner or an admin to make the move.
- **The colleague has already left the workspace** — their deals are still on
  the team board. An admin can move them, or any member can take them over one
  at a time.
- **Two people are working the same company** — say so and ask who keeps it.
  Do not merge anything on your own.
