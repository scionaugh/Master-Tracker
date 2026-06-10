# Scionaugh Website — Chat Context

## Goals and Purpose
Static marketing and artist site for Scionaugh (Bush Prog / Deep Daytime) and Sciotech (Psy-Techno / Bush Techno). Four pages: artist hub (index.html), course marketing (education.html), plugin advertisement (store.html), and purchase consent (consent.html). Deployed on Netlify, auto-deploys from GitHub on push to main.

**Role in the two-site architecture:** scionaugh.com advertises — sciosound.com sells. No checkout happens on this site.

## Key Decisions Made

**Two-site architecture:** scionaugh.com is marketing only. All purchases, consent, and downloads happen on sciosound.com. store.html becomes advertisement-only — CTAs link through to sciosound.com/store.

**Dual-project toggle:** Single site serves both Scionaugh and Sciotech identities. `setProject()` in script.js switches accent colour, hero image, logo, bio text, listen links, and label logo via CSS variable `--project-accent`.

**Education and Plugins removed from index.html:** They live on dedicated pages (education.html, store.html) only. Index is the artist hub.

**consent.html replaced with redirect:** consent.html on scionaugh.com now contains only a meta-refresh redirect to sciosound.com/store. The real consent and checkout live on sciosound.com. Decision resolved — redirect rather than delete, in case of bookmarks.

**Pricing confirmed:** $15 AUD per plugin / $50 AUD full bundle. Beta testers get lifetime access.

## Hosting & Deployment
- **Netlify:** scionaugh-website repo connected to Netlify, deployed as a static site (no build command, publish directory `.`). Auto-deploys on push to `main`.
- **DNS:** Migrated from GoDaddy to Netlify DNS. Nameservers updated in GoDaddy to dns1.p06.nsone.net, dns2.p06.nsone.net, dns3.p06.nsone.net, dns4.p06.nsone.net. SSL will auto-provision once propagation completes.
- **SETUP-LOG.md:** Deployment log with architecture decisions and full to-do list, produced this session — to be added to both this repo and sciosound-lms.
- GitHub is the single source of truth — Netlify pulls automatically on push.

## Constraints and Dependencies
- **Lemon Squeezy:** checkout happens on sciosound.com — scionaugh.com no longer holds any checkout URLs
- **Code signing:** plugins must be code-signed (Apple Developer Program for Mac, certificate for Windows) before public launch
- **GitHub repo:** `git@github.com:scionaugh/scionaugh-website.git` (SSH only — HTTPS does not work on this machine)

## Open Questions
- Choose mailing list platform (Mailchimp vs ConvertKit) for education.html signup field
- Patreon integration — deferred post-launch; what tier gets course access vs one-time buyers?
