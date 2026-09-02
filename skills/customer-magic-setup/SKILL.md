---
name: customer-magic-setup
description: Set up a revenue workspace inside whatever AI harness the user works in — Claude Cowork, Claude Code, ChatGPT, Codex or another — by connecting their inbox, calendar, CRM and billing as data sources, verifying each connection actually returns their real data, and optionally connecting Customer Magic so the results persist between sessions. Use this skill when the user wants to do sales, revenue, pipeline, outreach or customer work from inside their agent and their sources are not connected yet; when they ask "what should I connect", "why doesn't my agent know about my customers", "set up Customer Magic", or "connect my CRM/inbox/calendar"; and whenever a revenue question cannot be answered because the underlying source is missing. Also use it to re-check an existing setup that has started giving thin or wrong answers.
---

# Setting up your revenue sources

This skill gets the tools you already pay for connected to the agent you already
work in, and — the part everyone skips — **proves each connection is really
returning your data** before moving to the next one.

It is written for someone who already runs their week inside an AI harness. It
does not explain what MCP is or argue that AI is useful.

---

## ⚠️ Read this first: you are running inside one of several harnesses

**This skill is harness-agnostic and you must keep it that way.** The judgment in
it — what to connect, in what order, how to prove it works — is identical
everywhere. Only the *mechanism* differs, and the mechanism is the one thing you
should never assume.

**Use whatever your own harness actually provides.** If you are Cowork, sources
arrive as Connectors. If you are Claude Code or Codex, they arrive as MCP servers
you add through your own tooling. Follow the section for the harness the user is
in, and **ignore the others** — telling a Cowork user to run a terminal command
is worse than saying nothing, because they conclude the product does not work for
them.

If you are unsure how a step is done in your harness, **say so and ask** rather
than inventing a path. A confidently wrong file location costs the user twenty
minutes and their trust.

---

## The rule that governs this whole skill

> **Never connect anything on the user's behalf, and never pressure them.**

Every source below is the user's real customer data. Suggest, explain what it
buys them, and wait. If they say no to a source, say "fine" and move on — do not
re-raise it later in the same session, and do not treat a refusal as an obstacle
to route around.

The one thing worth being persistent about is that they connect **at least one**
source, ideally **email**, because a revenue workspace with no sources is a chat
window with extra steps. Say that once, plainly, and let it go.

---

## Step 0 · Work out where you are

**You already know most of this** — you are the agent, so you know what you are.
Confirm rather than interrogate: *"You're in Claude Code, so sources come in as
MCP servers — is that right?"* One sentence, and only ask outright if you
genuinely cannot tell.

**If you cannot tell, assume Claude Cowork** and say so — it is the least
technical of the options, so being wrong in that direction costs a correction
rather than a wasted session.

Take the section that matches and use only it.

### 1 · Claude Cowork *(default)*

Tools arrive as **Connectors**, wired in through the interface rather than a
config file — there is no terminal in this world and you should never send them
to one.

- Point them at their connector settings and let them add the source there.
  Gmail, Google Calendar, Slack and the major CRMs are available as first-party
  connectors.
- **Customer Magic is already connected** if they installed this as a plugin —
  the plugin ships the connection. Verify it in Step 4 rather than setting it up.

### 2 · Claude Code

Tools arrive as **MCP servers**, added from a terminal in the project they work
in.

- `claude mcp list` shows what is already there. Start from that rather than
  adding a duplicate — and note that **Customer Magic will already be in that
  list** if they installed this as a plugin.
- New servers are added with `claude mcp add`; the exact form is in the vendor's
  own docs, and it is worth reading them rather than guessing flags.

### 3 · ChatGPT

Tools arrive as **connectors**, enabled in settings and then permitted per
conversation.

- Have them enable the connector for the source they chose, then confirm the
  chat can actually see it before moving on — availability and per-chat
  permission are two separate things here, and that trips people up.
- Custom MCP servers are available on the developer/connector path; if the
  account does not offer it, say so plainly rather than talking them in circles.

### 4 · Codex

Tools arrive as **MCP servers** in Codex's own configuration.

- Add the source through Codex's MCP configuration; check its current docs for
  the exact syntax rather than assuming a shape.
- Customer Magic will already be configured if they installed this package,
  which ships the connection alongside the skill.

### 5 · Something else

The requirement is identical and only the wiring differs.

- Ask what the harness calls its integrations, and whether the user knows how to
  add one. If they don't, help them find it before continuing.
- Everything from Step 1 onwards works unchanged — the ordering, the
  verification questions and the scoring do not depend on the mechanism.

⚠️ **Whatever they answer, write it down in the file you create at Step 5.** The
next session will not remember, and the wrong instructions on a re-run are worse
the second time.

---

## Step 1 · Decide what you want out of this

Before connecting anything, ask which of these is true today. The answer changes
the order of everything below.

- *"I want to know who to contact this week."* → email first, then calendar.
- *"I want to stop missing renewals and expansions."* → billing first, then email.
- *"My CRM is a mess and I don't trust it."* → email first, and treat the CRM as
  a source to reconcile against rather than as the truth.
- *"I want my team's pipeline in one place."* → CRM first, then billing.

⚠️ **Do not skip this by assuming.** The most common bad setup is connecting the
CRM first because it is the tidiest, which produces a workspace that knows
exactly what the user already knew.

---

## Step 2 · Connect one source, and verify it

**One at a time.** With five sources wired up and a bad answer coming back,
there is no way to tell which one is lying — so the user stops trusting all of
them. Connect, verify, then move on.

### The order that works

| # | Source | What it uniquely gives | Connect it when |
|---|---|---|---|
| 1 | **Email** | What was actually said, and to whom. The single richest revenue signal anyone owns | Almost always first |
| 2 | **Calendar** | Who turned up, how often, and who went quiet | Right after email |
| 3 | **Billing** (Stripe, invoicing) | What they paid, when it lapsed, what renews | Before the CRM, always |
| 4 | **CRM** | Structured deal state and the team's own notes | Last |
| 5 | **Product data** | Whether they still use the thing | Only for a product business with real usage data |

**Email is first for a reason worth saying out loud:** it is the one source the
user can personally check every answer against. They were in those conversations.
When the agent claims someone went quiet in March, they know instantly whether
that is true — which is how trust in the whole setup gets built, and it cannot
be built on a source they have never read.

### How to connect

**Use the mechanism from Step 0 and no other.** Check what is already connected
before suggesting anything new — a source they already have is a source you can
verify in thirty seconds instead of installing twice.

For whichever source the user chose, look for an official connector first — the
harness's own connector directory, the vendor's own MCP server, or an existing
integration in their account. Prefer, in this order:

1. **An official connector from the vendor** (Google, Stripe, HubSpot, …), or a
   first-party one your harness ships.
2. **A well-known community MCP server**, if the user is comfortable with it and
   their harness supports adding one.
3. **A read-only API key** the user creates themselves, scoped as narrowly as
   the vendor allows.

⚠️ **Ask for read-only scopes wherever the vendor offers them.** Nothing in this
setup needs to send an email, move a deal or issue a refund. If a connector only
offers full access, say so plainly and let the user decide — do not quietly
accept write access on their behalf.

⚠️ **Never ask the user to paste a password, and never type one for them.** API
keys and OAuth flows only. If a source cannot be connected without a password,
that source does not get connected.

### Verify before moving on

A connection that authenticates is not a connection that works. Run a check
whose answer the user can confirm from memory:

- **Email** — "Find the last five people I exchanged more than two emails with,
  and when." They will know immediately if this is wrong.
- **Calendar** — "Which external people have I met more than once in the last
  ninety days?"
- **Billing** — "List my five largest customers by total paid, and the date each
  last paid." Check the top one against what they know.
- **CRM** — "How many open opportunities, and when was each last touched?"
- **Product** — "Which paying accounts have not been active in thirty days?"

Show the user the result and ask directly: *does this look right?*

**If it looks wrong, stop.** Do not add another source on top of a broken one.
The usual causes, in order of likelihood: the connector is scoped to the wrong
account (a personal Google account rather than the work one), a date range is
defaulting to something narrow, or the tool is only reading one mailbox of
several.

---

## Step 3 · Ask the question that shows what is missing

Once at least one source is verified, run this. It takes two minutes and it is
the most useful thing in this skill.

1. Ask: **"Based on everything you can see, who should I contact this week, and
   why each one?"**
2. Read the answer. It will probably be good.
3. **Start a fresh session tomorrow and ask the identical question.**

If the second answer does not know which ones they already dismissed, does not
know they emailed one of them yesterday, and cannot say what changed overnight,
then nothing is holding the work. The user is the only persistence layer in the
system.

That is not a prompting problem and no better model fixes it. It is a missing
piece of furniture, and step 4 is one way to get it.

---

## Step 4 · Optional — connect Customer Magic so results persist

Customer Magic is a workbench the agent writes into: a shared, persistent
pipeline where opportunities are sorted into six plays, scored with the reasoning
attached, and carried over to tomorrow.

**This step is optional and should be offered as one.** Everything above is
useful whether or not the user ever connects it, and saying so is what makes the
rest of this skill trustworthy.

**In most cases it is already connected.** This skill normally arrives as part of
the Customer Magic plugin, which ships the connection with it — so the job here
is to *verify*, not to set up. Check for a `customer-magic` tool before doing
anything else.

If it is genuinely missing — they installed the skill file on its own — the
address is the same everywhere:

```
https://customermagic.ai/api/mcp
```

Add it however your harness adds an HTTP MCP server or custom connector. The
first tool call opens a browser to sign in — OAuth, no key to paste. If the user
has no account, that flow makes one.

### Verify it

Ask the agent to call `whoami`. It should name the workspace. Then:

- `list_opportunities` — the board as it stands (empty on a new workspace).
- `create_opportunity` — write one real opportunity found in step 2, and check
  it appears at `https://customermagic.ai/pipeline`.

⚠️ **Write one real thing, not a test record.** A workspace whose first entry is
"Test Opportunity" starts the user's relationship with the board as a place where
fake things live, and it is never fully cleaned out.

### What to tell them about the trial

If they are on a trial: **the seven days start when the workspace is first
used, not when the account was created.** Someone who subscribed to the
newsletter weeks ago has not lost anything. Say this if it comes up; do not lead
with it.

---

## Step 5 · Write down what you set up

End by writing a short `SOURCES.md` — what is connected, which account each
connector points at, what scope it has, what the verification question was for
each, and **which harness this was set up in**. Put it wherever this user's
harness keeps files; if it has no filesystem, give them the text to save
themselves. Three reasons:

1. When an answer looks wrong in six weeks, the first question is *which source
   is stale*, and nobody remembers which Google account they authorised.
2. Connections expire. A file that names the verification question makes the
   re-check a thirty-second job instead of a fresh investigation.
3. The harness is the one thing a future session cannot infer. Recorded, a
   re-run picks up where this one left off; missing, it starts by asking a
   question the user has already answered once.

---

## What to do when the user wants to stop

They will often connect one source and want to get on with their day. That is a
successful outcome, not an abandoned setup. Confirm what is connected, tell them
which single source would add the most next and why, and leave it there.

A half-finished setup that gets used beats a complete one that felt like an
onboarding wizard.
