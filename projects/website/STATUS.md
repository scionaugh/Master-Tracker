# Scionaugh Website (scionaugh.com)

**Last Updated:** 2026-06-17
**Status:** ACTIVE — Live, two-site split complete, placeholder waitlist forms in place

## Current Focus
scionaugh.com is fully advertisement/marketing only. Placeholder waitlist forms exist on education.html (course) and store.html (plugin beta) — sciosound.com's store is now live, but these forms will intentionally stay as waitlists (going nowhere) until ready to direct traffic there. Booking form on index.html still needs Formspree. Mobile nav complete.

## Next Actions
- ~~Wire Formspree to booking form on index.html~~ DONE 2026-06-17 — used Netlify Forms instead (site is on Netlify, no 3rd-party account). After first deploy, confirm form shows in Netlify dashboard + set notification email.
- ~~Generate og-image.jpg (1200x630)~~ Template DONE 2026-06-17 (og-image-template.html, blocked from deploy). Still need to export the actual og-image.jpg and drop in repo root.
- ~~Copy course outline from sciosound store into education.html~~ DONE 2026-06-17 — full 18-module / 6-stage curriculum now live in the Curriculum panel; counts + journey section updated.
- Replace placeholder waitlist forms with links to sciosound.com when ready to direct traffic there (hold is intentional, not a dependency)
- Booking form also gained enquiry-type / event-date / location fields (commit 0f924aa). Local commits 4eefe5b + 0f924aa on main (ahead 2). USER must `git push origin main` from their machine — sandbox has no SSH keys/GitHub access. Netlify form detection confirmed ENABLED 2026-06-17.

## Blockers
- ~~sciosound.com DNS transfer needed~~ RESOLVED 2026-06-15: DNS already on Netlify. Email + mailing list just need MX/SPF/DKIM records added (pending email-host + list-platform choice).






## Recent Commits
- 2026-06-10 · 799e919 · Education + plugin page UX updates
- 2026-06-10 · 64fe2da · Sciotech mode: booking title and marquee follow project accent colour
- 2026-06-10 · a8b01a0 · Add mobile hamburger nav to all pages
- 2026-06-10 · 3c71323 · Plugins coming soon page, archive full store, remove music player
- 2026-06-10 · fadf99e · Two-site split: store.html advertisement-only, consent.html redirect, block dev files from Netlify
