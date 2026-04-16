# Design Director

An interactive design skill for Claude Code that turns plain-English requests into polished UIs by interviewing the user and applying brand design systems.

## What it does

Say things like:
- "let's design a new page"
- "build an admin dashboard screen"
- "redesign the pricing page"
- "make this look like Linear"

The skill asks 3-5 short numbered questions (what, who, vibe, emphasis, preview-first?) then builds the UI using one of 58 brand design systems — Linear, Stripe, Supabase, Airbnb, Notion, Vercel, Apple, Tesla, and more.

## How it works

- `SKILL.md` — the interview logic and anti-slop polish rules Claude follows
- `design-md/{brand}/DESIGN.md` — the full design system spec (colours, typography, spacing, shadows, components) for each of the 58 brands
- `design-md/{brand}/preview.html` / `preview-dark.html` — visual preview pages

The spec files live locally so the skill works offline — no service dependency.

## Brands included (58)

Organised into use-case menus inside the skill:

- **Admin / SaaS dashboards** — Linear, Supabase, Vercel, Stripe, Sentry, Notion, Superhuman
- **B2C consumer** — Airbnb, Apple, Spotify, Pinterest, Intercom, Uber
- **Dev tools / API docs** — Stripe, Vercel, Supabase, Cursor, Resend, PostHog, Raycast
- **Fintech** — Stripe, Wise, Revolut, Coinbase, Kraken
- **Premium / luxury** — Apple, Tesla, Ferrari, Lamborghini, BMW
- **AI/ML products** — Claude, Cohere, Mistral, x.ai, Runway, ElevenLabs
- **Productivity** — Notion, Linear, Cal, Superhuman
- **Plus** — Airtable, Clay, ClickHouse, Composio, Expo, Figma, Framer, HashiCorp, IBM, Lovable, MiniMax, Mintlify, Miro, MongoDB, NVIDIA, Ollama, OpenCode, Renault, Replicate, Sanity, Semrush, SpaceX, Together.ai, VoltAgent, Warp, Webflow, Zapier

## Attribution

Design spec files (`design-md/`) originate from [VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md) (MIT licensed) — rescued from pre-move git history before the upstream repo moved content to a hosted service.

Design systems are inspired by — not owned by or affiliated with — the brands they reference. Colours, fonts, and spacing may not be 100% accurate. These are starting points for building UIs in a similar language.

LICENSE file is included at the root.
