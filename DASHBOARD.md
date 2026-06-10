# 2026-06-10

## Check-in 4 — 11:07 UTC

### Projects

#### ScioSound LMS (sciosound.com)
**Status:** ACTIVE — Deployed on Netlify, DNS migration pending, Lemon Squeezy under verification. Store page live and shareable with beta testers per latest commits/todo (STATUS.md blocker list not yet reconciled — see Notes).

#### Ableton Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum scoped for Modules 1–17 (Module 18 placeholder); ready to start building Module 1

#### Sound Science Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum planning. 18-lesson table designed, no lessons built yet.

#### Scionaugh Website (scionaugh.com)
**Status:** ACTIVE — Two-site split complete; remaining work is wiring Formspree to the booking form

#### Mixing Suite
**Status:** ACTIVE — All four plugins at beta v2 with stochastic noise + UI updates; suite-level testing is the active focus.

| Plugin | Status | Current Focus |
|--------|--------|---------------|
| Sciotube | ACTIVE | Beta v2 installed — awaiting testing |
| Sciotape | ACTIVE | Beta v2 installed — awaiting testing |
| ScioGlue | ACTIVE | Beta v2 installed — awaiting testing |
| ScioSpace | ACTIVE | Beta v2 installed — awaiting testing |

### Blocked

| Project | Blocker |
|---------|---------|
| ScioSound LMS | Lemon Squeezy account under verification — products, pricing, webhook secret blocked |
| ScioSound LMS | Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) not set, pending Lemon Squeezy |
| ScioSound LMS | sciosound.com DNS still on GoDaddy — blocks email + SSL |
| Scionaugh Website | Waitlist forms blocked on sciosound.com waitlist pages (LMS project) |

### Open Todos

| Project | Open |
|---------|------|
| ScioSound LMS | 1 |
| Scionaugh Website | 1 |

**ScioSound LMS:**
- 2026-06-10 — Check Lemon Squeezy email for verification status. Once cleared, next session = Phase 14 (create products, fill placeholder maps, set secret).
  - Decide email provider: Google Workspace ($7/mo) vs Zoho (free)
  - Decide mailing list platform: Brevo vs Mailchimp (wire waitlist form)

**Scionaugh Website:**
- 2026-06-10 — Copy across course outline from sciosound store

### Notes
- LMS STATUS.md still lists "sciosound.com/store page incomplete" as a blocker, but the LMS TODO.md note (added since check-in 3) says the store page is live, member area works end-to-end, and sciosound.com/store.html can be shared with beta testers now. These two files disagree — worth confirming with the LMS project and updating STATUS.md if the store page is in fact done.
- Two new TODO items have been filed since the last check-in (LMS, Website) — both empty at check-in 3.
- No HIGH priority projects set; all treated as MEDIUM.
