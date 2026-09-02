# Customer Magic — agent plugin

Sets up your revenue sources in the agent you already work in, and connects
[Customer Magic](https://customermagic.ai) so what you find persists between
sessions instead of dying with the chat.

**Free, and useful whether or not you ever use the product.**

---

## What's in it

- **The setup skill** — connects your inbox, calendar, CRM and billing one at a
  time, and checks each one actually returns your real data before moving on.
  That check is the step everybody skips, and it's why most setups quietly
  produce confident nonsense.
- **The Customer Magic connection** — so the opportunities you find are held
  somewhere, ranked, and still there tomorrow.

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

Start a new session and say **"set up my revenue sources"**.

It opens by asking what you want to get out of it, because that changes the
order of everything after. Then one source — your inbox — verified against
something you can check from memory, before anything else is added.

## Read it first

You should read anything you install into an agent that can see your inbox.
It's one plain markdown file:
[`skills/customer-magic-setup/SKILL.md`](skills/customer-magic-setup/SKILL.md).

It connects nothing on your behalf, asks for read-only access wherever the
vendor offers it, and never asks you to paste a password.

---

Built by [Ben Sufiani](https://www.linkedin.com/in/bensufiani/) while building
Customer Magic. Questions: [customermagic.ai](https://customermagic.ai).

MIT.
