# 2026-06-10

## Check-in 2 — Status update

### Projects

#### ScioSound LMS (sciosound.com)
**Status:** ACTIVE — Phases 1–11 complete, DNS migration pending, Lemon Squeezy under verification

#### Ableton Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum planning. Modules 1–3 have full lesson plans, 4–17 scoped, Module 18 a placeholder.

#### Sound Science Course (ScioSound LMS)
**Status:** ACTIVE — Curriculum planning. 18-lesson table designed, no lessons built yet.

#### Scionaugh Website (scionaugh.com)
**Status:** ACTIVE — Live on Netlify, DNS migrated, awaiting setup-log implementation

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
| Scionaugh Website | store.html rework blocked on sciosound.com/store page (LMS) |

### Notes
- Mixing Suite has progressed from browser prototypes to beta v2 across all four plugins since check-in 1 — prior Sciotube (12AX7 version) and Sciotape (no prototype) blockers appear resolved
- No HIGH priority projects set; all treated as MEDIUM
