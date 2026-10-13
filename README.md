# Customer Magic — agent plugin

Sets up your revenue sources in the agent you already work in, and connects
[Customer Magic](https://customermagic.ai) so what you find persists between
sessions instead of dying with the chat.

**Free, and useful whether or not you ever use the product.**

---

## What's in it

- **A setup that remembers.** It connects your inbox, calendar, the place your
  sign-ups arrive and your CRM one at a time, and checks each one against
  something you know from memory before moving on. What is done is written
  down in Customer Magic, so the next session continues instead of starting
  over. A source you said no to is not raised again.
- **Four commands.**
  `/customer-magic:setup` continues the setup.
  `/customer-magic:review` goes through what waits for your decision, as
  multiple-choice questions, most important first.
  `/customer-magic:today` works today's follow-ups.
  `/customer-magic:status` says in five lines whether everything is connected
  and running.
- **Nine playbooks**, each started with one sentence:
  [bring a list in](https://customermagic.ai/playbooks/import-a-list),
  [keep a CRM in step](https://customermagic.ai/playbooks/crm-sync),
  [work today's list](https://customermagic.ai/playbooks/daily-run),
  [after a conversation](https://customermagic.ai/playbooks/after-a-conversation),
  [weekly review](https://customermagic.ai/playbooks/weekly-review),
  [close out an event](https://customermagic.ai/playbooks/event-close-out),
  [hand over deals](https://customermagic.ai/playbooks/hand-over),
  [review what comes in](https://customermagic.ai/playbooks/incoming-review),
  and going through that review in a conversation.
  Each one shows you what it is about to do before it writes anything.
- **Three background workers** (`agents/`): a scout that files who signed up,
  a fixer that proposes what to do with each, and a reviewer for overdue steps.
  They propose. They cannot decide, and they contact nobody.
- **The Customer Magic connection**, signed in through your browser. The
  plugin contains no key.

## Install

**Claude Code**

```
/plugin marketplace add bensufiani/customer-magic-plugin
/plugin install customer-magic@customer-magic
```

**Claude Cowork** — add the marketplace above, then install the plugin from it.
No terminal required.

**Codex, Cursor, Gemini CLI, GitHub Copilot and others**

```
npx skills add bensufiani/customer-magic-plugin
```

**ChatGPT** — there's no plugin format to install into. Paste
[the playbook](https://customermagic.ai/skill/customer-magic-setup/SKILL.md)
into a Project's instructions and every chat in that project will have it.

## Then

Start a new session and run **`/customer-magic:setup`**, or say "set up Customer Magic".

It opens by asking what you want to get out of it, because that changes the
order of everything after. Then one source — your inbox — verified against
something you can check from memory, before anything else is added.

## Read it first

You should read anything you install into an agent that can see your inbox.
Everything is plain markdown: the skills under [`skills/`](skills/), starting
with [`customer-magic-setup/SKILL.md`](skills/customer-magic-setup/SKILL.md),
the commands under [`commands/`](commands/) and the workers under
[`agents/`](agents/).

It connects nothing on your behalf, asks for read-only access wherever the
vendor offers it, and never asks you to paste a password.

---

Built by [Ben Sufiani](https://www.linkedin.com/in/bensufiani/) while building
Customer Magic. Questions: [customermagic.ai](https://customermagic.ai).

MIT.
