---
name: ask-people
description: Ask people a question and get structured answers back — answer buttons, short forms, confirmations, and after claiming, bookings, payments and automatic actions. For confirmations, choices, RSVPs, time slots, booking or order details, approvals, votes, feedback from a client, a team, customers or event guests. Use when the user needs an answer from someone else (a client, a team, customers, event guests) or wants a shareable question page, booking or sign-up link, or a form on a website. The question goes live immediately with a share link; answers are read back with the get_answers tool. No Answe account is needed.
argument-hint: "[who should answer and what you need to know, e.g. 'ask the team if Tuesday 10:00 works for the kickoff']"
---

# Ask people, get the answers back

Answe is a question-and-answer platform: someone asks a question, people answer it on a live page with answer buttons and a short form, and the asker gets the answers back. Call it a **question** when you talk to the user, never a poll, survey, form or widget: an Answe question can carry forms, bookings and automatic actions, and the user is asking people, not running a poll. The Answe MCP server (bundled with this plugin) hosts the question; nothing runs locally and no API key is needed.

If the user invoked this skill with arguments, treat `$ARGUMENTS` as the brief: who should answer, and what the user needs to know.

## Step 1 — Design the question

Call the Answe MCP tool `ask` (it appears with an `mcp__…answe__` prefix). Design before you call:

- **Always pass `asker_name`** — the person or business asking. Respondents see it as the sender ("Anna" or "Kalle's Bistro"). Ask for it if you can't infer it from the conversation or the project.
- **`question`**: short, warm, addressed to the respondent. "Can you make the kickoff on Tuesday at 10?" beats "Availability check".
- **1–3 `options`** (answer buttons). One primary answer with a form beats six buttons. Give secondary answers ("Not this time") a `thank_you_message` and no fields.
- **3–5 `fields` per form**, no more. Use `input_type` `select` with `choices` whenever the answer is one-of-N (party size, room, service) — selects convert better than free text. Use `date`/`time` for scheduling, `validation: "Email"` on email fields. Field `name`s are lowercase snake_case.
- **`thank_you_message`**: confirm what happens next, referencing fields with double braces and the `form.` prefix, e.g. `Thanks {{form.name}}! We'll confirm to {{form.email}}.` Only `{{form.<field_name>}}` is substituted — nothing else.
- **`notify_url`** (optional): if the user runs something that can receive an HTTPS POST, pass it and every answer is delivered there, signed, so nobody has to poll.

## Step 2 — Hand over the links

Always give the user these, clearly labeled, in this order:

1. **Share link** — send it to the people who should answer. It is a live page; it works in chat, email, a bio or a QR code right away.
2. **Owner link** — the user opens this to watch answers arrive live, share with one tap and edit the question. **Private**: it carries the owner token, so never hand it to respondents. Tell the user to keep it: pasted into any later conversation, it lets you read the answers again.
3. **Claim link** — saves the question into the user's own Answe account so answers also reach their inbox and the Answe app. **Single-use and private** — tell the user not to share it, and if the question is for a client, to forward it to the client who should own the answers. Unclaimed questions expire after ~30 days.

Mention the **embed code** only when the user has a website; see Step 4.

Keep the `question_id` and `answers_token` from the response for Step 3. Never show the token to respondents.

## Step 3 — Read the answers back

When the user wants to know what people answered, or is waiting for a reply, call `get_answers` with the question's `answers_token` (plus its `question_id` when you have it):

- **Several open questions in the conversation?** Recap them first ("You have two open questions: the kickoff time and Friday's dinner") and read the one the user means, or each in turn; one call reads one question.
- **New conversation, tokens gone?** Ask the user for the owner link they kept and pass it as `answers_token` exactly as pasted; the question is resolved from it.

- Pass `wait_seconds: 25` while the user is waiting; loop until `pending` is false. Each call returns the answers so far (oldest first), `count`, `pending` and `latest_answer_at`.
- Pass `latest_answer_at` back as `since` on the next call to get only newer answers.
- Report answers as what each person chose plus their form fields. Never invent an answer; "no answers yet" is a valid result.
- After the user claims the question the token stops working: configure the connection with an Answe API key (answe.com/account) and call `get_answers` with `question_id` only. With a key, `list_questions` and `get_dataset` also appear.

## Step 4 — Embed it (only when there is a website project)

**No website project open** (Cowork, or the user just wants a questionnaire, RSVP or sign-up they can send around): skip embedding entirely. Lead with the share link.

**Working in a codebase:** put the returned `embed_code` into the user's project — the part only you can do.

- Paste the snippet exactly as the tool returned it. It is two tags — a `<blockquote class="qstio-embed" data-widget-type="embed" data-qstio-id="…">` placeholder and one `<script async src="https://www.answe.com/assets/js/qstio_widget_loader.js">` loader. It works on any HTML site: WordPress themes, Squarespace/Wix code blocks, Pug/ERB/Blade templates, plain HTML.
- Place the blockquote where the question should render (contact section, booking page, footer CTA). The loader script belongs once per page, ideally before `</body>`.
- In React/Vue/Svelte apps, put the loader `<script>` in the HTML template (e.g. `index.html`) and render the blockquote markup in the component; the loader scans the DOM on load.
- Don't wrap the snippet in extra styling containers — it is responsive and brings its own styles.
- Say which file and line you placed it in.

If the project has no obvious place, ask where the user wants it rather than guessing a page.

## Status and editing

- To re-fetch links or check whether a question was claimed and how many answers it has, call `get_question` with its `question_id`. It never returns the token.
- `ask` cannot edit an existing question. For changes before anyone has answered, ask again and swap the `data-qstio-id` in any embed. After claiming, the owner edits the question in the Answe app (copy, fields, automatic replies, payments, full booking rules).
- Asking is rate-limited (10 questions/hour); get the design right in one call rather than iterating blindly.
