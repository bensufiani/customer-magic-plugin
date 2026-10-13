# Connecting the place sign-ups arrive

This is the source the background review reads. It is different for every
business, so ask before you suggest: *"When someone new signs up, registers or
fills in a form, where does that land first?"*

| It lands in | How an assistant usually reads it | Ask for |
|---|---|---|
| Their own product's database (Supabase, Neon, Postgres) | The vendor's connector, or a read-only database role | A role that can only read the tables with people and registrations |
| A payment or billing tool (Stripe) | The vendor's connector | A restricted, read-only key |
| An event tool (Luma, Eventbrite, a ticket shop) | The tool's connector or its export | Read access to guests and registrations |
| A form or survey tool (Typeform, Tally, Google Forms) | The connector, or the answer sheet behind it | Read access to answers |
| A spreadsheet | The drive connector | Read access to that one file |
| Only notification mails | The inbox they already connected | Nothing new: note the sender and subject to search for |

## Rules

- **Read only.** The review never writes to the source. If a connector offers
  only full access, say so and let them decide.
- **The live system, not a copy.** Ask which environment it is and check the
  newest entry against the real thing.
- **Say what personal data it is.** Sign-ups are other people's data. Tell the
  person that the review files name, email, company and what they did in
  Customer Magic, and that questionnaire answers are only used if they decided
  that is allowed. `get_trust_facts` has the details if they ask.
- **Never ask for a password**, and never put a key in a chat, a routine's
  instruction or a file in a repository. Keys go in the harness's own secret
  store.

When the five newest entries are right, call `record_setup_source` with kind
`signups`. Then the review itself is described with `get_playbook`
`incoming-review`, which also writes down how each source is read.
