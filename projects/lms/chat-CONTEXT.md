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

**Identity linking at checkout:** User must be logged in before hitting Lemon Squeezy. Checkout URL passes `checkout[custom][user_id]`. Webhook matches on user_id (preferred) or email fallback.

**Audio submissions removed (Phase 8):** Text-only activity submissions. `audio_file_path` column kept in schema for future use but audio upload removed — storage costs and instructor won't review audio drafts.

**Interactive apps deferred to Phase 13:** `interactive` block type replaced with static `image` block as placeholder. Apps (ADSR, step sequencer, signal flow, waveform) are post-launch.

**Video hosting: external embeds only.** YouTube/Vimeo unlisted. Avoids storage and bandwidth costs.

**New store page added to pre-launch scope:** `sciosound.com/store.html` not in the original LMS plan but identified as necessary before launch. Must be built and added as a phase before launch.

**Patreon integration deferred to Phase 14:** Post-launch only. Requires `patreon_id` column, OAuth flow, and `patreon-webhook.js`. Do not build until after defining what Patreon tier gets vs one-time buyers.

## Constraints and Dependencies
- **scionaugh.com (website project):** store.html rework on the website depends on sciosound.com/store being built here first
- **Lemon Squeezy:** account under verification — product creation and webhook setup blocked until approved
- **Supabase email confirmation:** disabled during Phase 3 testing — MUST be re-enabled before launch
- **Plugin code signing:** required before public launch (Apple Developer Program for Mac, certificate for Windows)

## Open Questions
- Price points for monthly subscription (early access) and one-time purchase (complete course) — not yet decided
- Plugin pricing confirmed ($15 AUD / $50 AUD bundle) but LS products not yet created
- What does the sciosound.com/store page look like — full storefront with course + plugins together, or separate pages?
- Patreon tier definition: what does a Patreon subscriber get vs a one-time course buyer?
