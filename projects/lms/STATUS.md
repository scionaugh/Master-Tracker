# ScioSound LMS (sciosound.com)

**Last Updated:** 2026-06-11
**Status:** ACTIVE — Deployed on Netlify, DNS migration pending, Lemon Squeezy under verification

## Current Focus
LMS build phases 1–11 complete. sciosound-lms repo connected to Netlify and deployed (publish directory `.`, functions directory `netlify/functions` from netlify.toml, auto-deploys on push to main). `_redirects` added to repo root to redirect `/` → `/members/login.html` since all HTML lives under `members/` with no root index.html. Store page (sciosound.com/members/store) is live with Plugins + Course tabs, confirmed working. Course pricing decided (see Notes). Lemon Squeezy requested additional verification info; reply sent 2026-06-10. Now implementing the remaining tasks from SETUP-LOG.md before launch.

## Pricing (decided 2026-06-10)
- Plugins: $15 AUD each, or $50 AUD full bundle. One-time purchase, lifetime access.
- Course Early Access (monthly subscription): $12 AUD/month, one new module released per month (18 total). Access continues only while subscribed — not lifetime.
- Course Complete (one-time, available once all 18 modules are released): $149 AUD, lifetime access.
- Founding-member idea floated (not yet finalised): tiered launch discount (e.g. first 10 buyers $129, next 10 $149) via discount codes against a higher list price, rather than separate LS products.

## Next Actions
- Migrate sciosound.com DNS from GoDaddy to Netlify — **first priority, blocks email + SSL + everything below**
- Set up hello@sciosound.com email (Google Workspace or Zoho) + add MX/SPF/DKIM to Netlify DNS
- Set up mailing list platform (Brevo or Mailchimp) under sciosound.com for course + plugin beta waitlists
- Build sciosound.com course waitlist page
- Build sciosound.com plugin beta signup page (becomes account registration once beta is live)
- Complete Lemon Squeezy verification, then create products, set pricing, get webhook secret
- Set Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) once Lemon Squeezy verified
- Add SETUP-LOG.md to repo root

## Blockers
- sciosound.com DNS still on GoDaddy — blocks email setup, SSL, and domain-verified sending
- Lemon Squeezy requested additional info before completing verification (demo/product links, pricing plan, social profile, product description) — replied 2026-06-10, awaiting their response
- Webhook at https://sciosound.com/api/lemon-webhook will not function until env vars are set








## Recent Commits
- 2026-06-11 · c1c7dac · Add plugin screenshots to member store cards
- 2026-06-11 · a0fd221 · Add plugin screenshot images for store
- 2026-06-10 · 790eb5d · Update CLAUDE.md and LMS-PLAN.md — session wrap-up (2026-06-10)
- 2026-06-10 · 065e4c6 · Make store page publicly accessible without auth
- 2026-06-10 · fd27cb1 · Add member Store page with Plugins + Course tabs
