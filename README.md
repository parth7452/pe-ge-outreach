# PE / Growth Equity Outreach Pipeline

A networking pipeline for Parth Pahuja's PE / Growth Equity full-time
recruiting — built to be operated from chat with Claude, with a data
tracker in this repo and outreach emails created as Gmail drafts.

Unlike the sibling [`startup-outreach`](https://github.com/parth7452/startup-outreach)
pipeline, this one is **user-driven, not autonomous**: there's no
scheduled research cycle. Parth gives Claude a firm name (and sometimes a
specific person's name) whenever he wants to reach out, and Claude
researches/drafts on demand.

## How it works

- **Input** — Parth gives a firm name, optionally with a specific
  person's name. If no name is given, Claude finds a suitable contact at
  the firm (see `CLAUDE.md`).
- **Research** — Claude finds the contact's title and a verified (or
  best-effort corroborated) work email, logging everything to
  [`data/targets.json`](data/targets.json).
- **Drafting** — Claude fills in the fixed email template (see
  `CLAUDE.md`) and creates it as a **Gmail draft**. Nothing is ever sent
  automatically — there is no "send" tool, only draft creation, so every
  email gets reviewed before it goes out. Parth attaches his resume PDF
  manually in Gmail before sending.
- **Sending** — Parth reviews drafts in Gmail and sends them himself.
- **Call prep** — when a call/coffee gets scheduled, Claude fills out
  [`templates/call_prep_template.md`](templates/call_prep_template.md).
- **Dashboard** — [`index.html`](index.html) is a static, read-only
  mobile page rendering the current pipeline. Enable via **Settings →
  Pages → Source: Deploy from a branch → `main` / `/ (root)`**, then
  live at `https://parth7452.github.io/pe-ge-outreach/`.

## Files

- `data/targets.json` — the tracker: every firm/contact, drafted email,
  and status.
- `templates/call_prep_template.md` — call prep outline.
- `index.html` — read-only status dashboard.
- `CLAUDE.md` — full operating instructions, including the fixed email
  template, for whichever Claude session is running this pipeline.

## Status values

`researched` → `drafted` → `sent` → `replied` → `call_scheduled` → `passed`

Claude sets the first two automatically. Parth (or telling Claude) updates
the rest as things progress.
