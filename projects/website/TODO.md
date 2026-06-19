# Scionaugh Website — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] NETLIFY CREDITS RESET ~2026-07-05 — production deploys frozen until then; batch all changes and push on/after Jul 5.
- [ ] 2026-06-17 — NETLIFY CREDITS root cause = Production deploys: 300 of 304.6 credits (20 deploys, ~15 credits/deploy). NOT telemetry (Supabase), NOT Agent Runners (0), bandwidth/web-requests trivial. DECISION: wait for billing reset + BATCH deploys (one push per session). Check reset date in Billing.
- [ ] 2026-06-17 — Booking form deployed but submissions BLOCKED until credit reset (live form shows fake "Got it", loses enquiries). Pending unpushed commits incl. subject-line + res.ok hardening (02f7eca) deploy on reset. Then set Forms notification email (scionaugh@gmail.com) + test.
- [ ] 2026-06-17 — PUSH local commits (4eefe5b, 0f924aa, 72a993b) to origin/main from your machine: `git push origin main`. Sandbox cannot push (no SSH keys).
- [ ] 2026-06-17 — After deploy: add email notification (scionaugh@gmail.com) under Netlify > Project configuration > Notifications > Form submission notifications.
- [ ] 2026-06-17 — In Netlify dashboard, confirm the "booking" form appears under Forms and set notification email (scionaugh@gmail.com) after first deploy.

## Done
- [x] 2026-06-17 — Wired scionaugh.com waitlist forms to Brevo (committed 5110bd5, NOT pushed): education.html course form -> Course Waitlist (list 3); store.html beta form -> Plugin Beta (list 2). Native-styled HTML POST to Brevo hosted-form serve URLs (target=_blank -> Brevo confirm page). Deploys with batch on Jul 5.
- [x] 2026-06-17 — Wired booking form (index.html) to Netlify Forms (AJAX POST, honeypot spam guard) instead of Formspree — no third-party account needed since site is on Netlify.
- [x] 2026-06-17 — Built og-image-template.html (1200x630, brand card, blocked from deploy in netlify.toml) for exporting og-image.jpg.
- [x] 2026-06-17 — Copied course outline from sciosound sheet into education.html: full 18-module curriculum across 6 stages (00-05), journey section + stat counts updated to match (was 17 modules / 8 stages).
