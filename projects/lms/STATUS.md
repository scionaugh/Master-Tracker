# ScioSound LMS (sciosound.com)

**Last Updated:** 2026-07-08
**Status:** ACTIVE — Deployed on Netlify; DNS already on Netlify; Lemon Squeezy APPROVED 2026-06-14. No external blockers left — email records + Phase 14 are the remaining work.

## Current Focus
LMS build phases 1–11 complete. sciosound-lms repo connected to Netlify and deployed (publish directory `.`, functions directory `netlify/functions` from netlify.toml, auto-deploys on push to main). `_redirects` added to repo root to redirect `/` → `/members/login.html` since all HTML lives under `members/` with no root index.html. Store page (sciosound.com/members/store) is live with Plugins + Course tabs, confirmed working. Course pricing decided (see Notes). Lemon Squeezy account APPROVED 2026-06-14 (verification cleared) — payment path now unblocked: create products, set pricing, get webhook secret, set Netlify env vars. LS constraint: products must be download-ready instantly on purchase (no manual/time-based fulfilment); use LS test mode for test purchases, not a real card. Now implementing the remaining tasks from SETUP-LOG.md before launch.

**Private beta (2026-06-19):** Plan is to run a private plugin-suite beta through the REAL pipeline — testers sign up, go through store + consent + LS checkout with a 100%-off code ($0), then download from members/plugins.html. Full plan documented in repo `PRIVATE-BETA-PLAN.md`. Store + LMS roster WIDENED this session from 4 plugins to all 9 (8 paid @ $15: ScioTape/Tube/Glue/Space/EQ/EQP/MB/Clip; ScioSee FREE for members). Bundle price being raised from $50 — shows TBC everywhere with TODO markers. Ownership model, webhook plugin-grant path, and My Plugins page already existed; only the roster needed widening + the actual download wiring remains.

## Pricing (decided 2026-06-10)
- Plugins: $15 AUD each, or $50 AUD full bundle. One-time purchase, lifetime access.
- Course Early Access (monthly subscription): $12 AUD/month, one new module released per month (18 total). Access continues only while subscribed — not lifetime.
- Course Complete (one-time, available once all 18 modules are released): $149 AUD, lifetime access.
- Founding-member idea floated (not yet finalised): tiered launch discount (e.g. first 10 buyers $129, next 10 $149) via discount codes against a higher list price, rather than separate LS products.

## Next Actions
- ~~Migrate sciosound.com DNS from GoDaddy to Netlify~~ DONE — verified 2026-06-15 on Netlify DNS (NS1 nameservers, SOA = domains+netlify.netlify.com)
- ~~Email host~~ DONE 2026-06-17: Zoho Mail Lite live, support@sciosound.com; MX + SPF + DKIM all verified green in Netlify DNS
- Mailing list DECIDED 2026-06-17: Brevo (free ~100k contacts, 300/day). TODO: create account + wire signup form via Brevo API
- Build sciosound.com course waitlist page
- Build sciosound.com plugin beta signup page (becomes account registration once beta is live)
- Create LS products (Phase 14), set decided pricing, get webhook secret — LS approved. Discount mechanics DECIDED 2026-06-17: course founding = single $149 product + capped fixed-amount code ($20 off); plugin friends-giveaway = 100%-off code scoped to plugin product(s), same checkout/fulfilment (verify with LS test-mode redemption)
- ~~Widen store/LMS roster to all 9 plugins~~ DONE 2026-06-19 (SQL CHECK + migration 11, webhook VALID_SLUGS/variant map, plugins.html, members/store.html, store.html, consent.html PRODUCTS/CHECKOUT_URLS)
- PRIVATE BETA remaining: (1) run supabase/11_user_plugins_roster.sql on live DB; (2) wire real signed-URL download on members/plugins.html (replace 'Coming Soon', mirror resources.html); (3) Phase 14 LS — create products + 100%-off code, fill VARIANT_PLUGIN_MAP + CHECKOUT_URLS, set bundle price (replace TBC); (4) verify $0 LS order fires order_created in test mode; (5) upload installers + plugin screenshots (ScioEQ/EQP/MB/Clip/See PNGs); (6) decide installer signing
- Set Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) once Lemon Squeezy verified
- Add SETUP-LOG.md to repo root

## Blockers
- ~~sciosound.com DNS on GoDaddy~~ RESOLVED 2026-06-15: already on Netlify DNS. Remaining email work is just adding MX/SPF/DKIM records (pending email-host choice), not a migration.
- Webhook at https://sciosound.com/api/lemon-webhook will not function until env vars are set











## Recent Commits
- 2026-07-08 · d3ca0d6 · chore(studio): graceful EADDRINUSE message + offline usage README
- 2026-07-08 · 7a7d02c · feat(studio): offline authoring studio + shared block renderers
- 2026-06-29 · 1b4f914 · feat(licensing): backfill ownership from user_plugins (beta grants)
- 2026-06-29 · 18ab6a7 · feat(validate): R25 in-plugin license check-in edge function
- 2026-06-11 · 4861d75 · Fix swapped ScioTape and ScioSpace images
