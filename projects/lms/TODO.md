# ScioSound LMS — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-19 — PRIVATE BETA: after Netlify reset, run supabase/11_user_plugins_roster.sql on live DB; wire real signed-URL downloads on members/plugins.html; set final bundle price (replace TBC in store + LS); add plugin screenshots; builds for ScioClip/ScioSee pending (stay 'Coming Soon'). See repo PRIVATE-BETA-PLAN.md.
- [ ] NETLIFY CREDITS RESET ~2026-07-05 — production deploys frozen until then; batch all changes and push on/after Jul 5.

- [ ] 2026-06-17 — Brevo wiring (BLOCKED on Netlify deploy freeze): embed the form on sciosound.com via the "Simple HTML" snippet (restyle to match site). NO API key / serverless function needed. Repeat for Plugin Beta. Wire when credits reset.
- [ ] 2026-06-17 — Course founding discount: in LS, keep single Complete Course at $149 list + create a capped fixed-amount code ($20 off -> $129, limited redemptions). Set during Phase 14.
- [ ] 2026-06-17 — Plugin giveaway: in LS, create a 100%-off code scoped to the plugin product(s), capped redemptions; friends use normal checkout (code entered or pre-applied via link), $0 order still delivers download + license. Verify fulfilment with one LS test-mode redemption.
- Note: nothing else blocking — site live, store live, member area works end to end.

## Done
- [x] 2026-06-19 — Store + LMS plugin roster widened 4 -> 9 (8 paid + ScioSee free) across SQL, webhook, plugins.html, both store pages, consent maps; PRIVATE-BETA-PLAN.md written. Pricing: ScioSee free, bundle price raised (TBC).
- [x] 2026-06-17 — Plugin Beta hosted form created (double opt-in, list 2). Public share URL: https://38c7d06e.sibforms.com/serve/MUIFACYu8wSovmUxnzekUlTOXxJU6n_wbbqN1gbHDXhkZgq-Vif7gTBntVEUDRbhZP4oMHY2kM-SuNsDXiE7b01kqnrb6LxRSePiVFy9YbRJpC6fCzEmEIgrtj23HanvIuTLDACPmPeKfyKuXXsytvNhptbiV9gHdDsXExcVGmJuwCYQHDUhTdaLVkO_R2_6Opkz93KhoXOGgeFRoA==
- [x] 2026-06-17 — Brevo account created (ScioSound org, login scionaugh@gmail.com). Lists: Course Waitlist (id 3), Plugin Beta (id 2), folder 'Waitlists'.
- [x] 2026-06-17 — Course Waitlist hosted form created (double opt-in). Public share URL (shareable NOW, independent of Netlify): https://38c7d06e.sibforms.com/serve/MUIFAAoY7cXbRDlL0LgRUAb-wYs3QjsFJtRxtU0KdIHKai9Ic00ReDBHBLNVTecxU1do3hsU9gy1BqfqEyqaHklpRvIuN2XtDnJv5Bl3eJI_cTNAQ7r-lVhuyHFwyhxzLmvPd2GySeZCDWzsx4hjAjw8-Jr8bI2YdLVPc7GKHCAgSlKMQChK2EU_8YvKfVPPQ7UrD_Zq2gFh4WTpKg==
- [x] 2026-06-17 — EMAIL DONE: Zoho Mail Lite live, support@sciosound.com; MX + SPF + DKIM all verified green in Netlify DNS. (Optional later: hello@/contact@ aliases.)
- [x] 2026-06-17 — DECIDED email host: Zoho Mail Lite (~$1/user/mo) over Zoho Free (web-only, no IMAP) and Google Workspace (~A$11/mo inc GST).
- [x] 2026-06-17 — DECIDED mailing list: Brevo (free ~100k contacts, 300 emails/day); Mailchimp free tier cut to 250 contacts / 500 sends/mo Jan 2026.
- [x] 2026-06-17 — DECIDED course founding mechanic: single $149 product + capped fixed-amount LS discount code (not per-tier products).
- [x] 2026-06-17 — DECIDED plugin friends/colleagues giveaway: 100%-off LS discount code on plugin product(s), same checkout + fulfilment as buyers (no separate free product).
- [x] 2026-06-10 -> resolved 2026-06-15 — Lemon Squeezy verification APPROVED 2026-06-14. Phase 14 operational next (create products with pricing, fill placeholder maps, set webhook secret). LS requires instant download-ready fulfilment; test via LS test mode.
- [x] 2026-06-10 — Decided course pricing: Early Access $12 AUD/month (access while subscribed), Complete Course $149 AUD one-time (lifetime access).
