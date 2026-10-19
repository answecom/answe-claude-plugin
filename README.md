# Answe plugin for Claude Code

Ask people a question from Claude — and get structured answers back.

[Answe](https://www.answe.com) is a question-and-answer platform: someone asks a question, people answer it on a live page with answer buttons and a short form, and the asker gets the answers back. With this plugin Claude can ask a client, a team, customers or event guests something — a confirmation, a choice, an RSVP, a time slot, booking or order details, feedback — send them the link, and read the answers back when they arrive. Questions are hosted on Answe's servers — **no account, sign-up, or API key needed to start**.

## What's inside

- **Answe MCP server** (`https://www.answe.com/mcp`, no authentication) with three tools:
  - `ask` — asks a question; returns the share link to send to respondents, a `question_id` + `answers_token` for reading answers back, a private owner link, a claim link and an optional website embed snippet
  - `get_answers` — reads the answers back (with `wait_seconds` to wait for a reply and `since` for only new ones); takes the `answers_token`, or an Answe API key for a claimed question
  - `get_question` — a question's status, answer count and links
  - with an Answe API key on the connection: `list_questions` and `get_dataset`
- **`/answe:ask-people` skill** — teaches Claude to design a good question (answer buttons, short forms, confirmation copy; bookings, payments and automatic actions after claiming), ask it, read the answers back, and — in a website project — embed it into the code. Claude also invokes it automatically when you ask for a form, a booking or sign-up link, or an answer from someone.

## Install

**Claude Code** — this repo is its own plugin marketplace:

```
/plugin marketplace add answecom/answe-claude-plugin
/plugin install answe@answe
```

**Claude Cowork** — **Customize** (sidebar) → **Plugins** → **Add marketplace** → `https://github.com/answecom/answe-claude-plugin` → install **Answe**.

Or try it locally from a clone of this repo:

```
claude --plugin-dir ./answe-claude-plugin
```

## Use

Just say what you need to know and from whom:

> Ask the team whether Tuesday at 10 works for the kickoff, and tell me when they've answered.

> Make an RSVP for Friday's dinner with a dietary preferences field — I'll send the link.

> Add a table reservation question for Kalle's Bistro to this page — party size, date and time.

or invoke the skill explicitly:

```
/answe:ask-people trial-class sign-up for my gym with a class picker
```

Claude asks the question and gives you:

- **Share link** — send it to the people who should answer; it is live right now
- **Owner link** — watch answers arrive live, share, edit (private)
- **Claim link** — save the question into an Answe account so answers also reach your inbox and the Answe app (single-use; keep it private, or forward it to the client who should own the answers)
- **Embed** — placed in your code when you are working on a website

Then ask Claude "what did they answer?" — it reads the answers back for you.

## For agents

An agent that needs a human's answer calls `ask`, sends the share link, and loops on `get_answers` with `wait_seconds: 25` until `pending` is false. Pass `notify_url` to `ask` to receive every answer as a signed `answer.created` POST instead of polling. After the question is claimed into an Answe account, read it with that account's API key (`Authorization: Bearer ansk_…`, created at answe.com/account).

## Notes

- Asking is rate-limited to 10 questions per hour per caller.
- Unclaimed questions expire after about 30 days.
- Documentation: [answe.com/docs/getting-started/ai-assistants](https://www.answe.com/docs/getting-started/ai-assistants)
- Privacy policy: [answe.com/privacy](https://www.answe.com/privacy) · Terms: [answe.com/terms](https://www.answe.com/terms)
- Support: answe@answe.com

## License

MIT © Loistava Interactive
