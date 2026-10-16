---
name: customer-magic-review-with-me
description: Go through the Customer Magic review together with the user, in the conversation. The background assistants have filed people who signed up or registered and overdue steps on the user's board, each with a proposal; this skill walks the user through them, most important first, asks with multiple-choice questions, and carries out each answer. Use this skill when the user says "let's do my review", "go through my cards", "what is waiting for me in Customer Magic", "review my incoming", "clean up my overdue steps with me", or points you at the review-with-me playbook. It decides nothing without the user's answer in this conversation.
---

# The review, together

Done means: nothing the user wanted to decide today is still waiting, and they
have heard in two or three sentences what changed on their board.

The user does not want to work through a screen of cards. They want you to
bring them the few things that need their head, take the rest off their hands
in batches, and do what they say. You are the interface.

---

## Rules

- **You decide nothing.** Every call to `decide_review` carries an answer the
  user gave in this conversation, for exactly the cards they gave it for. "Do
  what you think" is not an answer for a group marked `oneByOne`; ask.
- **Brief in the chat, then ask with the question tool, in the same turn.**
  The message carries what the user needs to decide; the question carries only
  the choice. If you have a tool that shows choices to tap (AskUserQuestion or
  the like), use it for every decision: one question per card or batch, two to
  four options, your recommendation first and marked. Never one without the
  other: a question with no brief makes them decide blind, and a brief that
  ends in "reply cut or keep" makes them type. Fall back to a numbered list
  only when you have no such tool.
- **Never show a list of cards and ask "what do you want to do?".** Say what
  it is, what is proposed and why, then ask.
- **"Nothing found" is not something you pass on.** A card the background
  assistant could not place is a card nobody has looked into yet. Look into it
  yourself before you bring it (step 3).
- **Important first.** The user may stop after five minutes; what they did not
  reach must be what mattered least. `list_review` gives the groups in that
  order: a key account, then the deals already on their board, then people new
  to them. A stranger who might be interesting never goes before a customer
  waiting on an answer.
- **Keep going.** After a card or a group, bring the next one. Never stop to
  ask "continue, or the batches?" and never recap where you stand halfway:
  the user stops you when they want to.
- **Say where you looked, and only what you did yourself.** "I checked the
  web" means you ran the search in this conversation. A background result you
  are repeating is the background's, and you say so.
- **Promise nothing for later.** Work an answer creates (a mail to write, a
  call to prepare) is either done now, in this conversation, or becomes the
  deal's step with a date. "I'll draft that afterwards" in the chat is lost
  when the conversation ends.
- **You contact nobody.** A decision changes the board. It sends nothing.
- **Never run this on a timer or without the user present.**

---

## Steps

### 1. Read

Call `list_review`. If nothing waits, say so in one sentence and stop.

Read every group before you speak. For a step card, read the deal
(`list_opportunities`) and its history, so you can say what the step was for
and what has happened since.

### 2. Open with the picture

Two or three sentences, no list of categories:

> 139 cards are waiting. 6 need your head: two key accounts and four deals
> proposed to drop. The other 133 fall into five kinds I can take as batches.
> Start with the six?

Do not promise a time. The ones that need their head take as long as they
take.

### 3. The ones that need their head, a few at a time

These are the groups with `oneByOne: true`: a key account, a proposal to drop
a deal, a card with no proposal.

**Before anything else: what stops working soon.** Read the reasons on the
step cards for a date that is days away (their event tomorrow, the next
session with a customer) or someone who has waited weeks for the user. Bring
those first, as a group of their own, whatever batch they sit in: after the
date, the step is worth nothing.

**Know the person before you bring the card.** For every card here, and always
when the proposal is missing or `unclear`:

1. Look in the user's own systems first, with whatever you can reach: have
   they bought, which events have they been to and since when, what did they
   answer in a form or questionnaire, have they written in, are they or their
   company already on the board, did they speak or host?
2. Then look them up on the open web by name and place: their role, their
   company. Use search and fetch tools; drive a browser only when nothing
   else reaches the page.
3. Say how sure the match is. A match by name and place is a guess: on the
   first real run it put a stranger at the wrong company. It becomes a fact
   only with something tied to the person's own address: their own answers,
   a reply, or a tool that finds the person behind an email address. If you
   have such a tool, use it before a name search; if it costs money, say what
   it costs before you use it and keep to the user's limit. Until then, write
   it on the deal as unconfirmed.
4. Two cards can be one person: the same name on the same day under a private
   and a work address. Bring them as one.

Never recommend "ignore" because nobody could be identified. Someone unknown
who keeps coming back is a reason to look harder, and often a deal.

**Bring up to four cards per message**, each with a short brief, then one
question per card in the same turn. The brief, as a few bullets:

- **Who:** the person, their role and company, with a link to where you read
  it.
- **Why it is here:** what came in, or what the overdue step was for.
- **History:** what they have done with the user, and what has happened since.
- **What each answer does** on the board, once, in plain words. Nothing is
  sent to anyone.
- **My take:** your recommendation and the one reason for it.

Options: your recommendation first, then the real alternatives for that kind
of card, then "Leave it for later".

- Someone new: add as a deal · add a note to the existing deal · ignore.
- An overdue step: push to the proposed date · keep overdue · drop the deal.

Carry each answer out with `decide_review` straight away, so a stop halfway
loses nothing. "Leave it for later" is no call at all.

**A new deal needs its first step.** When the user adds someone the card had
no step for, the result carries the deal's id (`dealId`): set the step you
agreed on with `set_next_action`, and put who the person is and the links you
found on the deal with `update_opportunity`. What you researched must not stay
in the chat.

While the user reads and answers, look into the next cards, so the next
message is ready when they are.

### 4. The batches

For each remaining group, largest first, one question:

> **41 students who signed up, no company.** Proposed: ignore. Examples: Lea M.
> (Uni Köln), Jonas P. (TH Wildau), Mira S. (no details).
> ○ Ignore all 41 (proposed) ○ Show me them first ○ Ignore all but… ○ Leave for later

- Always name three real examples, so the user can smell a wrong batch.
- Before you offer a batch to ignore, check its people against the user's own
  systems as in step 3. If any card stands out (a known company, a senior
  title, a past buyer, a regular, a person already on the board), pull it out
  and bring it alone before the batch.
- "Show me them first": list them compactly, one line each, then ask again.
- Send a batch to `decide_review` in calls of up to 50.

### 5. Read the results

`decide_review` answers per card. A card that comes back refused changed since
you listed it (the step was done meanwhile, the deal was closed). Say so in a
sentence; do not retry.

### 6. Close

Three sentences at most: how many new deals are on the board, how many steps
have a new date, what was left for later.

Then look at `handOver` from `list_review` (call it again: today's decisions
count). For each kind that is `ready`, ask one question:

> **Overdue, still to do: 5 in a row as proposed** (push the date). Let these
> run without you from now on? You would see them in the morning mail, and
> any of them can be undone.
> ○ Yes, let them run (recommended) ○ Not yet

On a yes, call `hand_over_kind` with that category and `alone`. Never for a
kind that is not `ready`: say how far it is instead ("3 of 5"). A drop, an
unclear case and a key account always still come to the user; say so if they
ask. Only a workspace admin can hand over; the call says so if they are not
one.

If they regret something in the same conversation, `undo_review` takes it
back.

---

## When it does not work

- **`person_only`** — you are connected with a workspace API key. Keys are for
  scheduled workers and cannot decide. Tell the user to connect this assistant
  by signing in, or to decide on the Review screen.
- **`not_earned`** from `hand_over_kind`: that kind has not gone as
  proposed often enough in a row yet. Say how far it is.
- **`billing`** — the workspace has no live subscription. Give them the
  billing link from the answer and stop.
