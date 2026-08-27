# Answe plugin for Claude Code

Create live booking forms, reservation flows, and question widgets from a prompt — and let Claude Code embed them straight into the site you're working on.

[Answe](https://www.answe.com) widgets are small chat flows: a visitor sees a question with answer buttons, fills a short form, and gets an instant confirmation. Every answer lands in the owner's Answe chat inbox. Widgets are created live on Answe's servers — **no account, sign-up, or API key needed to start**.

## What's inside

- **Answe MCP server** (`https://www.answe.com/mcp`, no authentication) with two tools:
  - `create_question_widget` — creates a live widget; returns a share link, a website embed snippet, and a claim link
  - `get_question_widget` — re-fetches a widget's status and links
- **`/answe:create-widget` skill** — teaches Claude to design a good widget (fields, selects, confirmation copy), create it, embed the snippet into your project's code, and hand over the links. Claude also invokes it automatically when you ask for a form or booking widget.

## Install

From the Claude Code plugin directory:

```
/plugin install answe
```

Or try it locally from a clone of this repo:

```
claude --plugin-dir ./answe-claude-plugin
```

## Use

Just describe what you need while working on a website project:

> Add a table reservation widget for Kalle's Bistro to this page — party size, date and time.

or invoke the skill explicitly:

```
/answe:create-widget trial-class sign-up for my gym with a class picker
```

Claude creates the widget, pastes the embed code into the right file, and gives you three links:

- **Share link** — the widget, live right now
- **Embed** — already placed in your code
- **Claim link** — open it to save the widget into a free Answe account so answers reach your inbox (single-use; keep it private, or forward it to the client who should own the answers)

After claiming, the widget is fully editable in the Answe app — copy, fields, automatic replies, [payments](https://www.answe.com/features/payments), and full [booking rules](https://www.answe.com/features/reservations-booking).

## Notes

- Widget creation is rate-limited to 10 per hour per caller.
- Unclaimed widgets expire after about 30 days.
- Documentation: [answe.com/docs/getting-started/ai-assistants](https://www.answe.com/docs/getting-started/ai-assistants)
- Privacy policy: [answe.com/privacy](https://www.answe.com/privacy) · Terms: [answe.com/terms](https://www.answe.com/terms)
- Support: answe@answe.com

## License

MIT © Loistava Interactive
