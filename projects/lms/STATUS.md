# ScioSound LMS (sciosound.com)

**Last Updated:** 2026-06-10
**Status:** ACTIVE — Deployed on Netlify, DNS migration pending, Lemon Squeezy under verification

## Current Focus
LMS build phases 1–11 complete. sciosound-lms repo connected to Netlify and deployed (publish directory `.`, functions directory `netlify/functions` from netlify.toml, auto-deploys on push to main). `_redirects` added to repo root to redirect `/` → `/members/login.html` since all HTML lives under `members/` with no root index.html. Now implementing the remaining tasks from SETUP-LOG.md before launch.

## Next Actions
- Migrate sciosound.com DNS from GoDaddy to Netlify (same process as scionaugh.com)
- Finish building out sciosound.com/store page — store.html and store/consent.html scaffolded with the full implementation outline (plugin listings, pricing, consent flow, login prompt before checkout) but not yet complete
- Complete Lemon Squeezy verification, then create products, set pricing, get webhook secret
- Set Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) once Lemon Squeezy is verified and Supabase credentials are confirmed
- Add SETUP-LOG.md to repo root

## Blockers
- Lemon Squeezy account under verification (business site submitted as sciosound.com) — products, pricing, and webhook secret blocked until approved
- Webhook at https://sciosound.com/api/lemon-webhook will not function until SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, and LEMON_SQUEEZY_SECRET are all set
- sciosound.com/store page (store.html + store/consent.html) is in progress but not finished — must be complete before either purchase flow (Flow A or Flow B) is functional end-to-end
- sciosound.com DNS still on GoDaddy — not yet migrated to Netlify



## Recent Commits
- 2026-06-10 · 52c7313 · Add Plugins nav link to all member pages
- 2026-06-10 · 251a418 · Phase 13–16 progress: store, DNS live, hardening partial
- 2026-06-06 · 6d1c110 · Add _redirects file
- 2026-06-06 · 77c9e8e · Initial commit — ScioSound LMS (Phases 1–12 complete)
