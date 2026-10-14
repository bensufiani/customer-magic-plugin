---
name: customer-magic-step-review
description: The overdue review in Customer Magic. A scheduled, unattended run that goes through the user's own overdue next steps and proposes for each one whether to push its date, drop the deal, or keep it as it is, for the user to confirm in one tap or by group. Use this skill when a routine or the user says "review my overdue steps", "clean up my overdue board", "run the overdue review", or points you at the step-review playbook. It changes nothing on its own unless the user handed that kind of case over, it never drops a deal, and it never contacts anyone.
---

# The overdue review

Done means: every overdue step on this person's board has a proposal with a
reason, and the run is finished with a note.

A board where most steps are overdue stops being read. The dates are no longer
true, so nothing on it says what to do today. Your job is to make every date
true again, one of three ways, and to let the user confirm it quickly.

You run on a timer with nobody watching. You propose; the user decides.

---

## Rules

- **Four answers, no others.** `push` the date, `replace` the step, `drop` the
  deal, or `keep` it overdue.
- **A drop is about the deal, not the step.** A customer whose kickoff has
  happened is not dropped because the kickoff step is stale. That is a
  `replace`.
- **You never drop a deal yourself.** A drop always waits for the user, even in
  a kind of case they handed over.
- **You contact nobody**, and you draft nothing to anyone.
- **You write nothing to the board.** No notes, no edits, no new steps. The
  only thing you write is the proposal.
- **Read before you propose.** A step's title alone does not tell you which of
  the three it is. The deal's history does.
- **The same kind of case always gets the same category.** The user decides,
  and later hands over, by category.
- **Key accounts are never done alone.** You may propose; it always waits.

---

## Step 1 · Start the run

Call `start_incoming_run` with `role: "reviewer"`. If it answers `paused`, stop.

## Step 2 · Get the overdue steps

Call `list_overdue_steps`. Each step comes with its deal: the person, the
company, where the deal stands and its last three events. Steps on deals that
are won or dropped are not listed. Skip every step where `alreadyProposed` is
true: a proposal for it is already waiting, or the user decided it and it has
not moved since.

If there are more than forty, take the forty oldest. The next run takes the
rest.

## Step 3 · Read each deal, then the thread behind it

The three events on each step are a start. Read more where they do not settle
it (`list_recent_activity`, `list_opportunities`).

Then open the mail thread, if you can reach the user's inbox and the deal
names one or names an address. This is where most of the answer is. A board
full of single cold mails looks the same on every card; the thread shows which
three got a reply the user has not answered. An unanswered reply is never a
`push` or a `drop`: it is a `keep`, and the reason says who is waiting.

If you cannot reach the inbox, say so once in the run's note. Your proposals
are then guesses from the board alone, and the user should know that.

## Step 4 · Decide what to propose

| What you find | Propose | Needs |
| -- | -- | -- |
| Still the right step, and nothing stops the user doing it | `push` | A date within the next two weeks. Spread them: not forty steps on one Monday. |
| The step waits on someone else who has not answered | `push` | A date that matches when an answer is realistic, and say who it waits on |
| The deal is alive but the step is not the step any more: the call, session or go-live it prepared has happened | `replace` | The new step as `title`, a date, and what happened to the old one |
| Two or more attempts, no answer, nothing new for weeks | `drop` | The reason, in one sentence the user can check against the history |
| The occasion has passed and the deal has no other life (a cold contact from one event, a role that is filled) | `drop` | What passed, and when. For a customer or anyone still in conversation this is a `replace`. |
| Something happened in the last few days (a reply, a booked call) | `keep` | What happened. The user should look at this one today. |
| You cannot tell | `keep` | Say what is missing. Never guess a drop. |

Categories that work: `overdue-still-to-do`, `overdue-waiting-on-them`,
`overdue-no-reply-twice`, `overdue-occasion-passed`, `overdue-something-happened`,
`overdue-step-outdated-deal-alive`.
Reuse the ones the workspace already has: `get_incoming_job` lists them.

## Step 5 · Propose

Call `propose_step` once per step, with the action, the category and a reason
of one or two sentences the user can check against the deal.

When the step only works before a date (their event, the next session with a
customer, a deadline), the reason starts with that date: "Their evening is on
15 Oct; after that the hook is gone." When someone has been waiting on the
user, it starts with who and for how long: "Christiane has waited seven weeks
for the four paragraphs." The user's assistant brings these first. If the answer
says `carriedOut: true`, the user handed that kind of case over and it is done.
Otherwise it waits for them in **Review**, shown only to them.

## Step 6 · Finish

Call `finish_incoming_run` with a note: how many steps you looked at, how many
of each proposal, and anything you could not read.

---

## Setting it up as a routine

Create one scheduled routine, once a night, in an environment that has the
Customer Magic connection (or its API key and the command-line client). Its
stored instruction is one paragraph of standing facts, then one pointer:

*"This board belongs to <name>, who asked for this review on <date>. Call
`get_playbook` with name `step-review` on the Customer Magic connection and
follow it."*

Run it once by hand and read its log before leaving it to the timer.

Then tell Customer Magic it is on a timer: `save_incoming_job` with
`reviewerEveryHours: 24`. From then on the user is warned, on the screen and
in the morning mail, when it has not run for two days. Without this a routine
that dies is silent.

## When something goes wrong

- **`not_overdue`** — the step's date has not passed. Leave it.
- **`no_date`** — a push or a replacement needs a date in the future.
- **`no_title`** — a replacement needs the new step in a few words.
- **`deal_closed`** — the deal is already won or dropped. Leave it.
- **A job is delivered and paid, and the deal is still open** — say so in a
  `keep`. Closing a deal as won is the user's to do.
- **`not_found`** — the step was completed or its deal changed owner while you
  worked. Leave it.
- **The user decides a card and it says the step changed** — they dealt with
  the step themselves in the meantime. Nothing was done, which is correct.
