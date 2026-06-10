# Scionaugh Website — Chat Context

## Goals and Purpose
Static marketing and artist site for Scionaugh (Bush Prog / Deep Daytime) and Sciotech (Psy-Techno / Bush Techno). Four pages: artist hub (index.html), course marketing (education.html), plugin advertisement (store.html), and purchase consent (consent.html). Deployed on Netlify, auto-deploys from GitHub on push to main.

**Role in the two-site architecture:** scionaugh.com advertises — sciosound.com sells. No checkout happens on this site.

## Key Decisions Made

**Two-site architecture:** scionaugh.com is marketing only. All purchases, consent, and downloads happen on sciosound.com. store.html becomes advertisement-only — CTAs link through to sciosound.com/store.

**Dual-project toggle:** Single site serves both Scionaugh and Sciotech identities. `setProject()` in script.js switches accent colour, hero image, logo, bio text, listen links, and label logo via CSS variable `--project-accent`.

**Education and Plugins removed from index.html:** They live on dedicated pages (education.html, store.html) only. Index is the artist hub.

**consent.html to be moved to sciosound.com:** The real consent and checkout now live on sciosound.com. consent.html on scionaugh.com is no longer part of the purchase flow — decision pending on whether to keep or remove it.

**Pricing confirmed:** $15 AUD per plugin / $50 AUD full bundle. Beta testers get lifetime access.

## Constraints and Dependencies
- **Depends on LMS project:** store.html rework requires sciosound.com/store page to exist first
- **Lemon Squeezy:** checkout URL in consent.html is a placeholder — needs replacing with real URL once LS account is verified
- **Code signing:** plugins must be code-signed (Apple Developer Program for Mac, certificate for Windows) before public launch
- **GitHub repo:** `git@github.com:scionaugh/scionaugh-website.git` (SSH only — HTTPS does not work on this machine)

## Open Questions
- Keep or remove consent.html from scionaugh.com entirely once sciosound.com takes over the purchase flow?
- Choose mailing list platform (Mailchimp vs ConvertKit) for education.html signup field
- Patreon integration — deferred post-launch; what tier gets course access vs one-time buyers?
