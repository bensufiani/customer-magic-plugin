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
- **Ask with the question tool, not with prose.** If you have a tool that
  shows the user choices to tap (AskUserQuestion or the like), use it for every
  decision. One question per decision, two to four options, the proposal first
  and marked as the recommendation. Fall back to a numbered list only when you
  have no such tool.
- **Never show a list of cards and ask "what do you want to do?".** Say what
  it is, what is proposed and why, then ask.
- **Important first.** The user may stop after five minutes; what they did not
  reach must be what mattered least.
- **You contact nobody.** A decision changes the board. It sends nothing.
- **Never run this on a timer or without the user present.**

---

## Steps

### 1. Read

Call `list_review`. If nothing waits, say so in one sentence and stop.

Read every group before you speak. For a step card on a deal you do not know,
read the deal (`list_opportunities`) so you can say in a clause what it is.

### 2. Open with the picture

Two or three sentences, no list of categories:

> 139 cards are waiting. 6 need your head: two key accounts and four deals
> proposed to drop. The other 133 fall into five kinds I can take as batches.
> About ten minutes. Start?

### 3. The ones that need their head, one at a time

These are the groups with `oneByOne: true`: a key account, a proposal to drop
a deal, a card with no proposal.

For each card, one question:

- Lead with who and what in one sentence, with the company.
- Then the proposal and its reason, in the assistant's words shortened to one
  sentence.
- Options: the proposal first, then the real alternatives for that kind of
  card, then "Leave it for later".
  - Someone new: add as a deal · add a note to the existing deal · ignore.
  - An overdue step: push to the proposed date · keep overdue · drop the deal.

Carry each answer out with `decide_review` straight away, so a stop halfway
loses nothing. "Leave it for later" is no call at all.

### 4. The batches

For each remaining group, largest first, one question:

> **41 students who signed up, no company.** Proposed: ignore. Examples: Lea M.
> (Uni Köln), Jonas P. (TH Wildau), Mira S. (no details).
> ○ Ignore all 41 (proposed) ○ Show me them first ○ Ignore all but… ○ Leave for later

- Always name three real examples, so the user can smell a wrong batch.
- If any card in the group stands out (a known company, a senior title, a
  person already on the board), pull it out and ask about it alone before the
  batch.
- "Show me them first": list them compactly, one line each, then ask again.
- Send a batch to `decide_review` in calls of up to 50.

### 5. Read the results

`decide_review` answers per card. A card that comes back refused changed since
you listed it (the step was done meanwhile, the deal was closed). Say so in a
sentence; do not retry.

### 6. Close

Three sentences at most: how many new deals are on the board, how many steps
have a new date, what was left for later. If the user agreed with every
proposal of one kind, tell them that the app offers to hand that kind over
after ten in a row, on the Review screen. Handing over is theirs to do there;
you cannot do it.

If they regret something in the same conversation, `undo_review` takes it
back.

---

## When it does not work

- **`person_only`** — you are connected with a workspace API key. Keys are for
  scheduled workers and cannot decide. Tell the user to connect this assistant
  by signing in, or to decide on the Review screen.
- **`billing`** — the workspace has no live subscription. Give them the
  billing link from the answer and stop.
