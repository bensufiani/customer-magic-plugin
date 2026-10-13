# How sources are connected, by harness

Read only the section for the harness the person is in. Telling a Cowork user
to run a terminal command is worse than saying nothing.

## 1 · Claude Cowork *(default)*

Tools arrive as **Connectors**, wired in through the interface rather than a
config file — there is no terminal in this world and you should never send them
to one.

- Point them at their connector settings and let them add the source there.
  Gmail, Google Calendar, Slack and the major CRMs are available as first-party
  connectors.
- **Customer Magic is already connected** if they installed this as a plugin —
  the plugin ships the connection. Verify it with `whoami` rather than setting it up.

## 2 · Claude Code

Tools arrive as **MCP servers**, added from a terminal in the project they work
in.

- `claude mcp list` shows what is already there. Start from that rather than
  adding a duplicate — and note that **Customer Magic will already be in that
  list** if they installed this as a plugin.
- New servers are added with `claude mcp add`; the exact form is in the vendor's
  own docs, and it is worth reading them rather than guessing flags.

## 3 · ChatGPT

Tools arrive as **connectors**, enabled in settings and then permitted per
conversation.

- Have them enable the connector for the source they chose, then confirm the
  chat can actually see it before moving on — availability and per-chat
  permission are two separate things here, and that trips people up.
- Custom MCP servers are available on the developer/connector path; if the
  account does not offer it, say so plainly rather than talking them in circles.

## 4 · Codex

Tools arrive as **MCP servers** in Codex's own configuration.

- Add the source through Codex's MCP configuration; check its current docs for
  the exact syntax rather than assuming a shape.
- Customer Magic will already be configured if they installed this package,
  which ships the connection alongside the skill.

## 5 · Something else

The requirement is identical and only the wiring differs.

- Ask what the harness calls its integrations, and whether the user knows how to
  add one. If they don't, help them find it before continuing.
- Everything from Step 1 onwards works unchanged — the ordering, the
  verification questions and the scoring do not depend on the mechanism.
