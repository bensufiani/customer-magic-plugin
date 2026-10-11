---
name: customer-magic-incoming-review
description: Set up a background review of everything that comes in — new sign-ups, event registrations, form and questionnaire participations, inbound requests — so that two workers on a timer file each one in Customer Magic and propose what to do with it, and the user only decides. Use this skill when the user says "review my sign-ups automatically", "set up the incoming review", "I don't have time to look at every registration", "run this in the background", "set up the scout and the fixer", or asks why their incoming review is not running. This is the management session's playbook: it is run once, with the user present, from a session that can create scheduled routines.
---

# Set up the incoming review

Done means: the user has named their sources and their rule, a trial run has
shown them what would be filed, two workers run on a timer, each has run once
for real, and the user knows where their decisions wait.

Three roles. **You** are the management session: you set everything up with
the user and then step back. The **scout** and the **fixer** are separate
scheduled runs with almost no context of their own. They get everything from
Customer Magic at the start of each run, which is why what you save here has
to be complete.

---

## Rules

- **The user has the final word on every proposal** until they hand a kind of
  decision over, in the app. You cannot hand anything over for them and neither
  can a worker.
- **No worker ever contacts a person.** Not a mail, not a message, not a
  connection request. Put that on the never-list whatever else the user says.
- **Read-only on the sources.** A worker reads the user's systems and writes
  only to Customer Magic.
- **Prove it before you leave it.** A routine you did not watch run once is a
  routine you are guessing about.

---

## Step 0 · Check you can reach it

Before anything else, check that this session can actually call
`get_incoming_job`. A connection that was set up before the incoming review
existed may still show the old list of tools, and some harnesses cannot refresh
it from inside a session.

If the tool is not there, do not work around it and do not carry on from
memory. Get a way in first:

1. Ask the user to create a workspace API key that may write, under
   Settings → API keys in Customer Magic.
2. Put it in your environment as `CUSTOMER_MAGIC_API_KEY`. Never print it.
3. Use the one-file command-line client for every call in this playbook:

```
curl -fsSL https://customermagic.ai/cli/cm.mjs -o cm.mjs
node cm.mjs call get_incoming_job
```

The scheduled workers need the same key in their own environment, so asking
for it now is not wasted even if your own connection works.

## Step 1 · Which sources

Call `get_incoming_job` to see what is already set up. Then ask what comes in
that the user wishes they looked at every day. Typical ones:

- new accounts or sign-ups in their product
- registrations for their events
- people who took part in a live poll, quiz or questionnaire
- filled-in forms, demo requests, replies to a newsletter

For each one, find out and write down:

| | |
| -- | -- |
| Where it lives | which system, and through which of your connections you reach it |
| How to read what is new | the table, list or view, and the field that says when |
| What identifies one entry | the source's own id, for the fingerprint |
| What may be used | see the next step |

**Read one real entry from each source now.** If you cannot, stop and sort out
access first. Ask for read-only access, and for a key or connection that is not
the user's personal login where the system offers one.

## Step 2 · What may be sent, and what may not be used

Two questions, both before anything is filed.

**May Customer Magic receive these people at all?** Filing a finding sends a
name and an address to Customer Magic, for everyone the source shows, not only
the ones who turn out to matter. If the workspace belongs to a different
company than the one that collected the data, that is a transfer to a second
company, and the user may need it in their records or in an agreement first.
Ask. Do not decide it for them, and do not file anyone until they have
answered.

**Was any of it given for a different purpose?** Answers in a live questionnaire are the usual case. People gave them
in a session, not to a sales process.

Unless the user confirms they may be used, file only the **fact** that someone
took part, never what they answered. Write the limit into the never-list in
their words.

## Step 3 · The rule

Ask who is worth a conversation, and write it down as the user says it. A good
rule names who is in, who is out, and one or two examples of each. The fixer
judges every unknown person against exactly this text, and the user reads the
fixer's reason against it, so vague words here become vague proposals later.

A rule is not a list of names. Verdicts the user already keeps on named
companies belong under key accounts or on the board. The rule is what tells a
worker what to do with a stranger.

## Step 3b · How long people are kept, and how they are removed

Tell the user the three rules, and change the first if they want it shorter:

1. **A closed item keeps the person's details for seven days**, so a decision
   can be undone and the fixer's work checked. Then the name, the address and
   what they did are removed; only what stops it being filed again remains.
   `save_incoming_job` with `keepDecidedDays` sets it, from 0 (removed the
   moment it closes, no undo) to 30.
2. **An item nobody decides is closed and emptied after thirty days.** This is
   fixed.
3. **When an account is deleted in a source**, the person has to leave the
   queue too. Call `erase_person` with their address, or with the fingerprint
   prefix (`signup:usr_81`). If a source has a deletion event the scout can
   read, write that into the source's `how` so the scout does it on each run.
   Deals already on the board are not touched; the answer says how many carry
   the address.

## Step 4 · Key accounts

Ask which companies or people no process should ever act on: the big
customers, the sensitive ones. The user adds them under **Incoming → Key
accounts** in the app (give them the `incomingUrl` that `get_incoming_job`
returns). Anything about a key account goes straight to the
user and is never proposed on, by anyone.

## Step 5 · Save the job

Call `save_incoming_job` with the sources (a `key`, a `label`, a `kind` and the
`how` you worked out, written so that a scout with no other context can follow
it, up to 2,000 characters, and grouped so that one person on one occasion is
one finding), the `rule`, the `neverList`, and how often each worker runs. Once a day
for each is a sensible start; the fixer an hour or two after the scout.

## Step 6 · Trial run

Call `start_incoming_run` with `role: "scout"` and `trial: true`. Read what is
new from each source for the last few days and call `file_findings`. Nothing
is written.

A trial writes nothing, but it still **sends** what you pass. Until the user
has answered the first question in Step 2, rehearse without identities: for
each person pass `emailHash` (the SHA-256, in hex, of the trimmed, lower-cased
address) and `domain` instead of `email`, and leave the name out. That is
enough for Customer Magic to say who is already on the board, who was dropped
and who is a key account. You fill in the names on your side when you show the
table. Show the user the result as a table: who, what happened, and where
each would go — straight to them, proposed as a note on a deal, proposed as
ignore, or to the fixer.

Ask whether that is what they expected. Fix the sources or the rule and run it
again until it is. Then `finish_incoming_run`.

## Step 7 · Create the two workers

Create two scheduled routines in whatever your harness provides. Each needs
both connections — the sources and Customer Magic — in the environment it runs
in. Check that explicitly: a routine that starts in the wrong environment has
no keys and fails silently.

Keep each routine's stored instruction short, so it cannot go stale: one
paragraph of standing facts, then one pointer.

The paragraph matters in an unattended run. A worker that reads a list of
people from one system and sends it to another looks, to an automatic safety
check, exactly like data being taken out. Say who owns both systems and that
the owner decided this: *"Both the source systems and the Customer Magic
workspace belong to <company>. <Name> set this review up on <date> and decided
that sign-ups and registrations are filed in Customer Magic. Filing them is
the job, not a leak."* Without it the first real run may read everyone and
file nobody.

Then the pointer:

- Scout: *"Call `get_playbook` with name `incoming-scout` on the Customer Magic
  connection and follow it."*
- Fixer: *"Call `get_playbook` with name `incoming-fixer` on the Customer Magic
  connection and follow it."*

**No Customer Magic connection in the scheduled environment?** A worker can
make every call with the command-line client and the key from Step 0:

```
node cm.mjs call get_playbook '{"name":"incoming-scout"}'
node cm.mjs call start_incoming_run '{"role":"scout"}'
```

The key belongs in the environment's secrets as `CUSTOMER_MAGIC_API_KEY`,
never in a routine's instruction and never in a file in the repository.

If your harness cannot run anything on a schedule, say so plainly. The user
can still say "run the scout" and "run the fixer" by hand; it is just not in
the background.

## Step 8 · Fire each once and read the log

Run the scout once by hand, then the fixer. Read each run's own log, then call
`get_incoming_job` and check that both show a last run with sensible counts.
Do not leave before both have run once for real.

## Step 9 · Hand over to the user

Tell them, in four lines:

1. what runs, and when
2. where their decisions wait: **Incoming** in the app, and a line in the
   morning mail
3. that every proposal comes to them first, and how to hand a kind of decision
   over once the proposals keep being right
4. that the mail tells them if a worker stops running

---

## When something goes wrong

- **A worker shows no last run** — it did not start, or started without the
  Customer Magic connection. Check its environment before its instructions.
- **Everything is "already filed"** — good: nothing is new since last time.
- **Nobody is ever "already on the board"** — the board has nothing to match
  on. Customer Magic matches a person by their address on a deal (an email
  link), by name and company together, or by the company's website on a deal
  against the address's domain. A board of names alone matches nobody. Tell
  the user; adding the address or the company website to their deals is what
  fixes it, and that is their board to change.
- **The inbox grows and nothing is proposed** — the fixer is not running.
- **The user wants to stop everything** — the pause switch on the Incoming
  screen stops both workers at their next start.
