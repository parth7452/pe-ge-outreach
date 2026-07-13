# PE / Growth Equity Outreach Pipeline — operating instructions

This repo is the data/tracking layer for Parth Pahuja's PE / Growth Equity
full-time recruiting outreach. The actual "app" is this repo + a Claude
session with Gmail/web-search access. Any Claude session picking up work
here should follow this playbook.

## Context on Parth

- Student at UT Austin (Finance & PPE).
- Currently interning at Rothschild & Co (per the email template — keep
  this as "currently interning" unless Parth says otherwise, since the
  template wording is fixed).
- Previous internship at Crestview Partners.
- Recruiting for full-time Private Equity / Growth Equity roles.
- Contact info used in this pipeline's signature: 469-988-9008,
  parthpahuja@utexas.edu. **This is a different signature/email than the
  `startup-outreach` pipeline** (which uses parthpahuja@gmail.com and a
  different sign-off) — don't mix the two up.

## Workflow

This pipeline is **user-driven, not autonomous** — unlike
`startup-outreach`, there is no scheduled research cycle. Parth triggers
each round himself, one of two ways:

- **Firm name only** → research the firm to find a suitable person to
  reach out to (see "Finding a contact" below).
- **Firm + specific person's name** → skip the "who to contact" research,
  go straight to finding that person's contact info and drafting.

For each new target:

1. **Check `data/targets.json` first** — skip or flag duplicates (same
   firm + person already present).
2. **Finding a contact** (only when Parth didn't name someone): default
   to junior-to-mid-level people — Analysts, Associates, VPs — rather
   than Partners/MDs, since they're statistically far more likely to
   reply to a student's cold email. Exception: if Parth names a specific
   senior person, or explicitly asks to target more senior people at a
   firm, do that instead. If a UT Austin alumni connection exists at the
   firm, prefer them and note the connection in `notes` (it's not part of
   the email body itself — the template doesn't reference it — but it's
   useful context and may be worth flagging to Parth).
3. **Find the person's work email**, using the same verification
   approach as the sibling pipeline:
   - Search `"[Name]" email contact` — RocketReach/ZoomInfo listings
     often show a masked email (e.g. `j******@firm.com`) confirming
     domain + first letter.
   - Search `"[Firm]" email format` — sites like RocketReach/Prospeo
     sometimes state the firm's email format directly.
   - Cross-reference: name + masked email + confirmed domain agreeing on
     a pattern is enough to use `firstname@domain` or
     `firstinitiallastname@domain` (match whatever pattern is confirmed).
   - **Never invent an email with zero corroborating evidence** — leave
     it `null` and set `outreach_channel: "linkedin"` instead.
4. Fill out the schema entry in `data/targets.json` (see below).
5. **Draft the email using the fixed template below.** Substitute only
   `[first name]` and `[firm name]` — do not alter the wording,
   structure, or add per-firm personalization beyond those two
   placeholders. This template is intentionally generic/repeatable;
   don't "improve" it unless Parth explicitly asks for a one-off variant.
6. Create a **Gmail draft** via the Gmail MCP tool (`create_draft`,
   `htmlBody` so "Parth Pahuja" in the signature renders bold) — never
   attempt to send directly, there is no send tool. If no email was
   found, leave `to` unset, don't create a Gmail draft, and store the
   drafted copy in the tracker with `outreach_channel: "linkedin"`.
   Store the returned draft ID back into `targets.json`.
7. Set `status: "drafted"`. Don't overwrite `sent` / `replied` /
   `call_scheduled` / `passed` once Parth has moved a target past
   drafting — those are his to update.
8. Commit and push the updated `data/targets.json` to this repo.
9. Report back in chat: who was drafted, and flag anyone where no email
   could be found (recommend LinkedIn instead of guessing).

## Email template (fixed — do not deviate from the wording)

**Subject:** `Quick Chat Request - UT Austin Student Interested in [Firm]`
(swap in the firm name; ask Parth if he ever wants a different subject
line convention).

**Body:**

```
Hi [first name],

I hope this email finds you well! For some context, my name is Parth Pahuja, and I am a student at UT Austin, currently interning at Rothschild & Co., with a previous internship at Crestview Partners.

I recently learned about [firm they work at] from a mentor, and I wanted to learn more about your experience at the firm and your background.

If you have just 15-20 minutes to spare over the coming weeks, I'd love to find a time to connect over the phone/coffee to speak about the firm, your experience, and your background. I've attached a copy of my resume in case it's helpful. I'm looking forward to hearing from you!

Best,
Parth
—
Parth Pahuja   (bold this line — use htmlBody with <b> when creating the
                Gmail draft; plain-text fallback can use **bold** markdown)
The University of Texas at Austin | Finance & PPE
469-988-9008 | parthpahuja@utexas.edu
```

**No resume attachment** — the Gmail draft tool doesn't support
attachments, and Parth has confirmed he'll attach the resume PDF himself
in the Gmail app before sending. Keep the "I've attached a copy of my
resume" line in the draft text as-is.

## Call prep

If Parth reports a call/coffee scheduled with a target, use
`templates/call_prep_template.md` to generate a filled-in one-pager and
send it to him directly in chat (no need to commit these to the repo
unless he asks to keep it).

## Data schema (`data/targets.json`)

Each entry represents **one person at one firm** (not grouped by firm —
each email is addressed to a single individual, unlike the startup
pipeline's multi-founder emails):

```
{
  "id": "firstname-lastname_firm-slug",
  "firm": "...",
  "person": {
    "name": "...",
    "title": "...",
    "email": "... or null",
    "linkedin": "... or null",
    "source": "url used to find them/their contact info"
  },
  "how_sourced": "given by Parth" | "researched",
  "ut_austin_alum": true | false | "unknown",
  "outreach_channel": "email" | "linkedin",
  "status": "researched" | "drafted" | "sent" | "replied" | "call_scheduled" | "passed",
  "gmail_draft_id": "... or null",
  "draft_subject": "...",
  "draft_body": "...",
  "date_added": "YYYY-MM-DD",
  "date_last_action": "YYYY-MM-DD",
  "notes": "free text"
}
```

## Dashboard

`index.html` (repo root, not `/docs`) is a static, read-only mobile
dashboard that reads `data/targets.json` via a same-directory relative
fetch and renders it as cards grouped by status. It's served via GitHub
Pages with **Source: Deploy from a branch → `main` / `/ (root)`** — it
must stay at the repo root and Pages must stay set to root, not `/docs`,
for the same reason as the sibling repo. It has no write access to
anything — all actions happen through chat with Claude.
