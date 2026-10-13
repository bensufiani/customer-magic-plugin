# The check question for each source

A connection that signs in is not a connection that works. After connecting a
source, run its question, show the answer, and ask: *does this look right?*
Pick a question whose answer the person knows from memory.

| Source | Ask the source | Why this one |
|---|---|---|
| Inbox | "The last five people I exchanged more than two emails with, and when." | They were in those conversations. |
| Calendar | "Which people outside my company have I met more than once in the last ninety days?" | They remember who they keep meeting. |
| Where sign-ups arrive | "The five newest sign-ups or registrations, with date and what they signed up for." | They can open the real system and compare. |
| CRM | "How many open deals are there, and when was each last touched?" | They know roughly how many. |
| Billing | "My five largest customers by total paid, and when each last paid." | They know the top one. |

## When the answer looks wrong

Stop. Do not connect another source on top of a broken one. The usual causes,
most likely first:

1. The connector points at the wrong account: a private Google account
   instead of the work one.
2. A date range defaults to something narrow.
3. Only one of several mailboxes, calendars or pipelines is being read.
4. For sign-ups: a test database or a staging environment instead of the live
   one.

Record it with `record_setup_source`, status `failed`, and what was wrong in
`note`. Fix it, run the question again, record `proven`.
