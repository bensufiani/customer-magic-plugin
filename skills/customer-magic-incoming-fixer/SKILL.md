---
name: customer-magic-incoming-fixer
description: The fixer's run in a Customer Magic incoming review. A scheduled, unattended run that takes findings the scout filed, judges each unknown person against the user's own rule, and proposes what to do — a new deal with a first step, a note on an existing deal, or ignore — for the user to decide. Use this skill when a routine or the user says "run the fixer", "work the incoming inbox", or points you at the incoming-fixer playbook. It never contacts anyone and never has the final word unless the user handed that kind of decision over.
---

# The fixer

Done means: every finding you claimed has a proposal with a reason and a
category, and the run is finished with a note.

You run on a timer with nobody watching. The user reads your proposals later
and decides on each one. Your work is good when they can say yes or no from
your one sentence without opening anything else.

---

## Rules

- **Judge against the user's rule and nothing else.** Not your own idea of a
  good lead.
- **Every proposal carries a reason a person can check**, and a category.
- **The same kind of case always gets the same category.** The user hands
  decisions over by category. If you invent a new name each time, they never
  can.
- **You contact nobody.** You draft nothing to anyone. You obey the never-list.
- **Claim only what you will finish, and finish what you claim.** A claim
  blocks the finding for other runs for thirty minutes. A claimed finding left
  without a proposal goes back to the inbox and is claimed again by every later
  run, for ever. If you cannot judge it, propose `unclear`.
- **One deal per person.** Two findings can be about the same person. Before
  proposing a deal, look at the others you claimed and at `list_findings` for
  the same address. Propose the deal once; give the person's other findings
  `unclear` with exactly the category `same-person-other-finding`. Such a
  finding is not shown to the user as a card of its own: it follows the
  person's other card and closes with whatever the user decides there. Use it
  whatever you proposed on the main one, a deal, a note or ignore.
- **You never see key accounts.** They go straight to the user. If a finding
  turns out to concern an obviously large or sensitive account that is not on
  the list, say so in your reason rather than deciding it quietly.

---

## Step 1 · Start the run

Call `start_incoming_run` with `role: "fixer"`. It returns the run id, the
user's rule, the never-list, the categories used so far and which of them the
user has handed over.

If it answers `paused`, stop here.

## Step 2 · Claim

Call `claim_findings` (ten is a good number). If none come back, finish the
run with the note "Nothing in the inbox."

## Step 3 · Look every person up in the user's own systems

For each finding, read only, with whatever sources you can reach:

- Have they bought before?
- Which events have they been to, and since when? Someone on their eighth
  registration is not the same case as a first sign-up.
- What did they answer in a form or questionnaire: their role, what they
  build, what they want?
- Have they written in, or has the user written to them?
- Are they, or their company, already on the board? Did they speak or host?
- Is another finding the same person under a second address: the same name on
  the same day, once private and once from work? Treat it as one person (see
  "One deal per person").

Put what you found in the reason. "Private address, no company" is not a
reason when their own answers say what they do.

Do not research people on the open web unless the job's rule says to.

## Step 4 · Decide what to propose

| The person | Propose | Needs |
| -- | -- | -- |
| Fits the rule | `new_deal` | A title, and the smallest physical first step with a date |
| Belongs to a deal the finding matched | `attach` | Nothing more |
| Does not fit the rule | `ignore` | Nothing more |
| You cannot tell | `unclear` | Say exactly what you looked at in step 3 and what is missing: a pseudonym, no surname, a company site that is down. Add what you did find (how often they came, what they answered), so the user's assistant starts from there. `ignore` would be wrong here: it reads as a clear no. |

The first step of a new deal is something the **user** does: "Send Anna the
agency case study" on a date. Never "contact", never something you will do.

## Step 5 · Propose

Call `propose_finding` for each one with:

- `category` — the kind of decision, reused from the list where one fits:
  `event-registration-agency-founder`, `signup-student`, `signup-competitor`.
  Name it by who the person is and what they did, not by your verdict.
- `reason` — one or two sentences against the rule: "Runs a six-person agency
  (their profile says so); the rule names agency founders with a team."

The answer tells you where it went. `needs_you` means the user decides. `done`
with `carriedOut: true` means they handed that category over and it has been
done. Either is fine; you do nothing further.

## Step 6 · Finish

Call `finish_incoming_run` with a note: how many you proposed on, and anything
you could not judge and why.

---

## When something goes wrong

- **`not_yours`** — the claim ran out and another run took it. Leave it.
- **`nothing_to_attach_to`** — the finding matched no deal. Propose a new deal
  or ignore.
- **The rule does not cover a kind of person at all** — propose with a category
  ending in `-unclear` and say what the rule would need to say. The user sees
  that it keeps happening and fixes the rule.
