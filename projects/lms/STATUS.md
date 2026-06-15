# ScioSound LMS (sciosound.com)

**Last Updated:** 2026-06-11
**Status:** ACTIVE — Deployed on Netlify; DNS already on Netlify; Lemon Squeezy APPROVED 2026-06-14. No external blockers left — email records + Phase 14 are the remaining work.

## Current Focus
LMS build phases 1–11 complete. sciosound-lms repo connected to Netlify and deployed (publish directory `.`, functions directory `netlify/functions` from netlify.toml, auto-deploys on push to main). `_redirects` added to repo root to redirect `/` → `/members/login.html` since all HTML lives under `members/` with no root index.html. Store page (sciosound.com/members/store) is live with Plugins + Course tabs, confirmed working. Course pricing decided (see Notes). Lemon Squeezy account APPROVED 2026-06-14 (verification cleared) — payment path now unblocked: create products, set pricing, get webhook secret, set Netlify env vars. LS constraint: products must be download-ready instantly on purchase (no manual/time-based fulfilment); use LS test mode for test purchases, not a real card. Now implementing the remaining tasks from SETUP-LOG.md before launch.

## Pricing (decided 2026-06-10)
- Plugins: $15 AUD each, or $50 AUD full bundle. One-time purchase, lifetime access.
- Course Early Access (monthly subscription): $12 AUD/month, one new module released per month (18 total). Access continues only while subscribed — not lifetime.
- Course Complete (one-time, available once all 18 modules are released): $149 AUD, lifetime access.
- Founding-member idea floated (not yet finalised): tiered launch discount (e.g. first 10 buyers $129, next 10 $149) via discount codes against a higher list price, rather than separate LS products.

## Next Actions
- ~~Migrate sciosound.com DNS from GoDaddy to Netlify~~ DONE — verified 2026-06-15 on Netlify DNS (NS1 nameservers, SOA = domains+netlify.netlify.com)
- Set up hello@sciosound.com email (Google Workspace or Zoho) + add MX/SPF/DKIM in Netlify DNS — ~10 min once host chosen; no MX records exist on the domain yet
- Set up mailing list platform (Brevo or Mailchimp) under sciosound.com for course + plugin beta waitlists
- Build sciosound.com course waitlist page
- Build sciosound.com plugin beta signup page (becomes account registration once beta is live)
- Create LS products (Phase 14), set decided pricing, get webhook secret — LS approved, ready to do now
- Set Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) once Lemon Squeezy verified
- Add SETUP-LOG.md to repo root

## Blockers
- ~~sciosound.com DNS on GoDaddy~~ RESOLVED 2026-06-15: already on Netlify DNS. Remaining email work is just adding MX/SPF/DKIM records (pending email-host choice), not a migration.
- Webhook at https://sciosound.com/api/lemon-webhook will not function until env vars are set









## Recent Commits
- 2026-06-11 · 4861d75 · Fix swapped ScioTape and ScioSpace images
- 2026-06-11 · c1c7dac · Add plugin screenshots to member store cards
- 2026-06-11 · a0fd221 · Add plugin screenshot images for store
- 2026-06-10 · 790eb5d · Update CLAUDE.md and LMS-PLAN.md — session wrap-up (2026-06-10)
- 2026-06-10 · 065e4c6 · Make store page publicly accessible without auth
