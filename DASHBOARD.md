# 2026-06-10

## Check-in 3 — 10:45 UTC

### Projects

#### ScioSound LMS (sciosound.com)
**Status:** ACTIVE — Phases 1–11 complete, now implementing Phases 13–16 (store, DNS, hardening) from SETUP-LOG.md before launch

#### Ableton Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum fully scoped for Modules 1–17 (Module 18 a placeholder); ready to start building Module 1

#### Sound Science Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum planning. 18-lesson table designed, no lessons built yet.

#### Scionaugh Website (scionaugh.com)
**Status:** ACTIVE — Two-site split complete (store.html now advertisement-only, redirects to sciosound.com/store); remaining work is wiring Formspree to the booking form

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
| ScioSound LMS | Netlify env vars (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) not set |
| ScioSound LMS | sciosound.com/store page incomplete |
| ScioSound LMS | sciosound.com DNS still on GoDaddy |

### Open Todos
None — all project todo inboxes are empty.

### Notes
- Scionaugh Website's prior blocker (store.html rework pending LMS store page) is resolved via the two-site split: store.html is now advertisement-only and no longer depends on the LMS store page.
- LMS commit "251a418 — Phase 13–16 progress: store, DNS live, hardening partial" suggests DNS may now be live, but STATUS.md still lists DNS migration as pending and as a blocker — worth confirming and updating if resolved.
- No HIGH priority projects set; all treated as MEDIUM.
