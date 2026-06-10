# ScioSound LMS — Chat Context

## Goals and Purpose
Full-control member platform at sciosound.com. Music production course delivered as sequential modules with progress tracking, activity submissions, and a downloadable resources library. Also the canonical storefront for the Scionaugh Mixing Suite plugins. Vanilla HTML/CSS/JS frontend, Supabase backend, Netlify hosting.

**Role in two-site architecture:** sciosound.com sells — scionaugh.com advertises. All purchases, consent, downloads, and course access happen here.

## Key Decisions Made

**Two pricing tiers:**
- Early access: monthly subscription (Lemon Squeezy), one new module per month
- Complete course: one-time purchase once all modules released
- Both set `membership_tier = 'paid'` in Supabase via webhook. Cancellation flips back to `free`.

**Module unlock logic:** Sequential by `order_index`. A module is accessible if all previous modules have `completed_at` set, or `is_free_preview = true`, and tier requirement is met. M01 is always free.

**Identity linking at checkout:** User must be logged in before hitting Lemon Squeezy. Checkout URL passes `checkout[custom][user_id]`. Webhook matches on user_id (preferred) or email fallback. Already implemented in `netlify/functions/lemon-webhook.js`.

**Audio submissions removed (Phase 8):** Text-only activity submissions. `audio_file_path` column kept in schema for future use but audio upload removed — storage costs and instructor won't review audio drafts.

**Interactive apps deferred to Phase 13:** `interactive` block type replaced with static `image` block as placeholder. Apps (ADSR, step sequencer, signal flow, waveform) are post-launch.

**Video hosting: external embeds only.** YouTube/Vimeo unlisted. Avoids storage and bandwidth costs.

**New store page added to pre-launch scope:** `sciosound.com/store.html` not in the original LMS plan but identified as necessary before launch. Must be built and added as a phase before launch. Should include: full plugin listings, pricing, consent flow, and login prompt before checkout.

**sciosound.com owns all waitlist/signup capture:** Both the course waitlist and plugin beta signup belong to sciosound.com. scionaugh.com has placeholder email forms that will be replaced with links to sciosound.com pages once built. Setup sequence: DNS transfer → hello@sciosound.com email → mailing list platform (Brevo/Mailchimp) with domain auth → build waitlist pages → update scionaugh.com links.

**Email address needed:** hello@sciosound.com (Google Workspace or Zoho). DNS transfer to Netlify is the first blocker — MX/SPF/DKIM records go into Netlify DNS once it's migrated.

**Patreon integration deferred to Phase 14:** Post-launch only. Requires `patreon_id` column, OAuth flow, and `patreon-webhook.js`. Do not build until after defining what Patreon tier gets vs one-time buyers.

## Hosting & Deployment
- **Netlify:** sciosound-lms repo connected to Netlify, deployed as a static site (publish directory `.`, functions directory `netlify/functions` picked up from netlify.toml). Auto-deploys on push to `main`.
- **_redirects:** Added to repo root, redirects `/` → `/members/login.html` — necessary because all HTML files live in the `members/` subfolder with no root index.html.
- **DNS:** sciosound.com still registered on GoDaddy — not yet migrated to Netlify DNS (scionaugh.com migration already done; same process required here, see website project).
- **SETUP-LOG.md:** Deployment log with architecture decisions and full to-do list, produced this session — to be added to both this repo and scionaugh-website.

## Lemon Squeezy
- Account created, business website submitted as sciosound.com. Currently under verification — no products created yet.
- Lemon Squeezy is the merchant of record (handles international GST/VAT).

## User Flows (confirmed)
- **Flow A (via scionaugh.com):** scionaugh.com/store.html (ad) → sciosound.com/store → login/signup prompt → consent → Lemon Squeezy checkout with user_id → account upgraded → access unlocked
- **Flow B (via sciosound.com directly):** sign up free → dashboard → plugins section → sciosound.com/store → login confirmed → consent → Lemon Squeezy checkout with user_id → account upgraded → access unlocked

## Constraints and Dependencies
- **scionaugh.com (website project):** store.html rework on the website depends on sciosound.com/store being built here first
- **Lemon Squeezy:** account under verification — product creation and webhook setup blocked until approved
- **Supabase email confirmation:** disabled during Phase 3 testing — MUST be re-enabled before launch. Part of Phase 12 (security hardening), which is flagged as non-optional before launch
- **Plugin code signing:** required before public launch (Apple Developer Program for Mac, certificate for Windows)

## Open Questions
- Price points for monthly subscription (early access) and one-time purchase (complete course) — not yet decided
- Plugin pricing confirmed ($15 AUD / $50 AUD bundle) but LS products not yet created
- What does the sciosound.com/store page look like — full storefront with course + plugins together, or separate pages?
- Patreon tier definition: what does a Patreon subscriber get vs a one-time course buyer?
