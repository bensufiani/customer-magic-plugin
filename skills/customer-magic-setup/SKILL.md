---
name: customer-magic-setup
description: Set up Customer Magic inside whatever AI harness the user works in (Claude Code, Claude Cowork, ChatGPT, Codex or another): first get three deals with a dated next step on their board from names they tell you, then connect their inbox, calendar, the place sign-ups arrive and their CRM one at a time, prove each one returns their real data, and switch on the background review. It remembers what is already done, so it continues where the last session stopped. Use this skill when the user says "set up Customer Magic", "continue the setup", "what is still missing", "what should I connect", "connect my inbox/calendar/CRM", "why doesn't my agent know about my customers", when a revenue question cannot be answered because a source is missing, and to re-check a setup that has started giving thin or wrong answers.
---

# Setting up Customer Magic

Done means: `get_setup` says `ready`, and every optional step is either done or
the person said no to it.

This skill gets the tools the person already pays for connected to the agent
they already work in, proves each one returns their real data, and writes that
down in Customer Magic so the next session continues instead of starting over.

It is written for someone who already runs their week inside an AI harness. It
does not explain what MCP is or argue that AI is useful.

---

## Rules

- **Never connect anything on the person's behalf, and never pressure them.**
  Suggest, say what it buys them, and wait. A no is recorded as `skipped` and
  not raised again.
- **One source at a time, each one checked.** With five sources wired up and a
  bad answer coming back, nobody can tell which one is lying.
- **Read access wherever the vendor offers it.** Nothing in this setup needs to
  send a mail, move a deal or issue a refund.
- **Never ask for a password, and never type one.** OAuth and keys the person
  creates themselves only. A key never goes into a chat, a file in a repository
  or any Customer Magic field.
- **Use the mechanism of the harness you are in, and no other.** If you are
  unsure how a step is done here, say so and ask. `resources/harnesses.md` has
  one section per harness; read only yours.
- **The board before any source.** On a new workspace the first ten minutes are
  three deals with a dated next step, from names the person tells you. Never
  send them to a terminal, a settings page or another app before that: in
  Claude Code, adding Gmail means leaving the conversation and restarting it,
  and a new user who is sent there first leaves with an empty board.
- **A half-finished setup that gets used beats a complete one that felt like a
  wizard.** They may stop after any step.

---

## Steps

### 1. Read where they stand

Call `whoami`, then `get_setup`.

- No Customer Magic tools at all: the connection is missing. The address is
  `https://customermagic.ai/mcp`; add it the way this harness adds a connector
  (`resources/harnesses.md`). The first call opens a browser to sign in. There
  is no key to paste, and someone without an account gets one in that flow.
- `whoami` names the wrong workspace: `list_organizations`, then
  `select_organization`, before anything is written.

Say in two sentences what is done and what you would do now. Not the whole
list:

> Your inbox is connected and checked, and the board has 12 deals. Next I
> would connect your calendar, about three minutes. Go?

**On a new workspace `next` is the board.** Ask, in the same message:

> Who are three people you are talking to about buying from you right now? A
> name, the company and one sentence each is enough.

Create each deal with `create_opportunity`, propose one next step with a date
per deal in the same reply, and set them with `set_next_action` once they
agree. That is the first ten minutes. A spreadsheet or CRM export instead of
names: `get_playbook` with `import-a-list`.

Only once the board has its three deals, ask the one question that sets the
order of the sources: what do they want out of this?

- "Know who to contact this week": inbox, then calendar.
- "Stop losing sign-ups and registrations": inbox, then where sign-ups arrive.
- "My CRM is a mess": inbox first, and treat the CRM as something to check
  against, not as the truth.

### 2. Do the step in `next`

Each step in `get_setup` has a `how`. For a source it is always the same four
moves:

1. **Look first.** Check what this harness already has connected. A source
   they already have is checked in thirty seconds instead of installed twice.
2. **Connect** with an official connector from the vendor or the harness.
   Second choice: a well-known community one, if they are comfortable. Third: a
   read-only key they create themselves.
3. **Check** with the question in `resources/check-questions.md`, show the
   answer and ask whether it is right.
4. **Record** with `record_setup_source`: the kind, the product, the account it
   points at, `proven`, `failed` or `skipped`, and the harness you are in.

For where sign-ups arrive, read `resources/signup-sources.md` first. It is the
one source that is different for every business.

If connecting a source means leaving this conversation (a settings page
elsewhere, a restart), say what it adds and let them pick the moment. "Later"
is an answer: record nothing, and carry on with what they came for.

The steps that are not sources:

- Three dated deals: the question in step 1; a file goes through
  `get_playbook` with `import-a-list`.
- The morning mail: theirs to confirm in the app, one button; give them the
  link in `mailSettingsUrl`.
- The background review and its timers: `get_playbook` with `incoming-review`.
  Run it with the person present. The scheduled runs it creates must sign in
  with a workspace API key, never with the person's own sign-in.
- A CRM in step: `get_playbook` with `crm-sync`.

### 3. Read it again

Call `get_setup` after every step and take the new `next`. Stop when:

- `ready` is true and every optional step is done or skipped, or
- they want to get on with their day. Then say what is connected and which
  single step would add the most next, and leave it.

### 4. Show them it works

Once the board has deals, ask the question the setup is for: **"Who should I
contact this week, and why each one?"** Answer it from the board, and from the
inbox too once it is connected. That answer is the proof, not the checklist.

---

## Coming back later

- **`recheck`**: a source was last checked more than 45 days ago. Run its
  question again and record the result. Connections expire without saying so.
- **An answer looks thin or wrong**: `get_setup` shows which account each
  source points at. The first suspect is the wrong account, the second an
  expired connection.
- **A worker is late**: the step "Put the scout and the fixer on a timer" says
  which one. Open its last run in the harness and read the log before changing
  anything.

## What to tell them about the trial

If they are on a trial: the seven days start when the workspace is first used,
not when the account was created. Say this if it comes up; do not lead with it.
