---
name: create-widget
description: Create a live Answe question widget (booking form, reservation flow, contact or sign-up form) and embed it into the current website project. Use when the user wants a form, booking widget, reservation flow, lead-capture, sign-up, or contact widget on a site — theirs or a client's. The widget goes live immediately with a share link, an embed snippet, and a claim link; no Answe account is needed to create it.
argument-hint: "[what the widget is for, e.g. 'table reservations for Kalle's Bistro']"
---

# Create an Answe widget and embed it

Answe turns a question into a live chat widget: a visitor sees a question with answer buttons, fills a short form, gets an instant confirmation — and every answer lands in the owner's Answe inbox. The Answe MCP server (bundled with this plugin) creates real, hosted widgets. Nothing runs locally and no API key is needed.

If the user invoked this skill with arguments, treat `$ARGUMENTS` as the widget brief.

## Step 1 — Design the widget

Call the Answe MCP tool `create_question_widget` (it appears with an `mcp__…answe__` prefix). Design before you call:

- **Always pass `business_name`** — the business or person the widget is for. It becomes the widget's visible identity ("Kalle's Bistro" with an avatar). Ask for it if you can't infer it from the project.
- **`question`**: short, warm, visitor-facing. "Which meeting room would you like to book?" beats "Booking form".
- **1–3 `options`** (answer buttons). One primary action with a form beats six buttons. Give secondary options (like "Opening hours") a `thank_you_message` and no fields.
- **3–5 `fields` per form**, no more. Use `input_type` `select` with `choices` whenever the answer is one-of-N (party size, room size, service) — selects convert better than free text. Use `date`/`time` for scheduling, `validation: "Email"` on email fields. Field `name`s are lowercase snake_case.
- **`thank_you_message`**: confirm what happens next, referencing fields with double braces and the `form.` prefix, e.g. `Thanks {{form.name}}! We'll confirm your table to {{form.email}} shortly.` Only `{{form.<field_name>}}` is substituted — nothing else.

The tool returns three things: a **share link** (live immediately), an **embed code**, and a **claim link**.

## Step 2 — Embed it into the project

This is the part only you can do: put the returned embed snippet into the user's codebase.

- The snippet is two tags — a `<blockquote class="qstio-widget" data-widget-id="…">` placeholder and one `<script async src="https://www.answe.com/assets/js/qstio_widget_loader.js">` loader. It works on any HTML site: WordPress themes, Squarespace/Wix code blocks, Pug/ERB/Blade templates, plain HTML.
- Place the blockquote where the widget should render (contact section, booking page, footer CTA). The loader script belongs once per page, ideally before `</body>`.
- In React/Vue/Svelte apps, put the loader `<script>` in the HTML template (e.g. `index.html`) and render the blockquote markup in the component; the loader scans the DOM on load.
- Don't wrap the snippet in extra styling containers — the widget is responsive and brings its own styles.

If the project has no obvious place, ask where the user wants it rather than guessing a page.

## Step 3 — Hand over the links

Always give the user all three links, clearly labeled:

1. **Share link** — the widget is already live there; good for testing and for link-in-bio use.
2. **Embed** — where you placed it in the code (say which file and line).
3. **Claim link** — the user (or their client) opens this to save the widget into a free Answe account so incoming answers reach their chat inbox at answe.com. **Claim links are single-use and private** — tell the user not to share it, and if the site is for a client, to forward it to the client who should own the answers. Unclaimed widgets expire after ~30 days.

## Editing and status

- To re-fetch a widget's links or check whether it was claimed, use the `get_question_widget` tool with its id.
- The create tool cannot edit an existing widget. For changes before anyone has answered, create a fresh widget and swap the `data-widget-id` in the embed. After claiming, the owner edits the widget in the Answe app (copy, fields, automatic replies, payments, full booking rules).
- Creation is rate-limited (10 widgets/hour); prefer getting the design right in one call over iterating blindly.
