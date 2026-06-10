# ScioSound LMS (sciosound.com)

**Last Updated:** 2026-06-10
**Status:** ACTIVE — Deployed on Netlify, DNS migration pending, Lemon Squeezy under verification

## Current Focus
LMS build phases 1–11 complete. sciosound-lms repo connected to Netlify and deployed (publish directory `.`, functions directory `netlify/functions` from netlify.toml, auto-deploys on push to main). `_redirects` added to repo root to redirect `/` → `/members/login.html` since all HTML lives under `members/` with no root index.html. Now implementing the remaining tasks from SETUP-LOG.md before launch.

## Next Actions
- Migrate sciosound.com DNS from GoDaddy to Netlify — **first priority, blocks email + SSL + everything below**
- Set up hello@sciosound.com email (Google Workspace or Zoho) + add MX/SPF/DKIM to Netlify DNS
- Set up mailing list platform (Brevo or Mailchimp) under sciosound.com for course + plugin beta waitlists
- Build sciosound.com course waitlist page
- Build sciosound.com plugin beta signup page (becomes account registration once beta is live)
- Finish building out sciosound.com/store page — store.html and store/consent.html scaffolded but not yet complete
- Complete Lemon Squeezy verification, then create products, set pricing, get webhook secret
- Set Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) once Lemon Squeezy verified
- Add SETUP-LOG.md to repo root

## Blockers
- sciosound.com DNS still on GoDaddy — blocks email setup, SSL, and domain-verified sending
- Lemon Squeezy account under verification — products, pricing, and webhook secret blocked until approved
- Webhook at https://sciosound.com/api/lemon-webhook will not function until env vars are set
- sciosound.com/store page not yet complete — blocks both purchase flows end-to-end






## Recent Commits
- 2026-06-10 · 790eb5d · Update CLAUDE.md and LMS-PLAN.md — session wrap-up (2026-06-10)
- 2026-06-10 · 065e4c6 · Make store page publicly accessible without auth
- 2026-06-10 · fd27cb1 · Add member Store page with Plugins + Course tabs
- 2026-06-10 · 52c7313 · Add Plugins nav link to all member pages
- 2026-06-10 · 251a418 · Phase 13–16 progress: store, DNS live, hardening partial
