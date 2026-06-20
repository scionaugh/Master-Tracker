# 2026-06-15

## Check-in — daily dashboard (regenerated)

### Projects

#### ScioSound LMS (sciosound.com)
**Status:** ACTIVE — Deployed on Netlify. **No external blockers left:** Lemon Squeezy APPROVED 2026-06-14 and DNS confirmed on Netlify (verified 2026-06-15). Remaining work is all in-hand: pick email host + add MX/SPF/DKIM records (~10 min), run Phase 14 (create LS products at decided pricing, set webhook secret), set Netlify env vars. LS requires instant download-ready fulfilment; test via LS test mode, not a real card.

#### Ableton Course (ScioSound LMS)
**Status:** ACTIVE — All 18 modules scoped to template (Module 18 a placeholder); ready to build Module 1 ("The Digital Canvas").

#### Sound Science Course (ScioSound LMS)
**Status:** ACTIVE — Full 18-lesson curriculum table designed. No lessons built yet; needs build order + a pilot lesson.

#### Scionaugh Website (scionaugh.com)
**Status:** ACTIVE — Live; two-site split + mobile nav done. DNS blocker resolved. Remaining: wire Formspree booking form, generate og-image.jpg, add email/mailing-list records (shared with LMS).

#### Patreon Integration
**Status:** ACTIVE — Planning only. LS no longer a blocker; now gated only by LMS Phase 12 + needing a Patreon Creator account with tiers defined.

#### Mixing Suite (9 plugins) — **0.2.0 · β5 (0.2 line COMPLETE)**; next = 0.3.0
**Status:** ACTIVE — All committed on develop. Heavy work landed 2026-06-15.

| Plugin | Status | Current Focus |
|--------|--------|---------------|
| Sciotube | ACTIVE | Beta v2 — awaiting testing |
| Sciotape | ACTIVE | Beta v2 awaiting testing; dry/wet Mix wired (Phase D, efe3464) |
| ScioGlue | ACTIVE | Beta v2 awaiting testing; numeric GR readout added (Phase G, 1538a80) |
| ScioSpace | ACTIVE | Convolution rooms beta v3 implemented (Phase H); host tuning next |
| ScioEQ | ACTIVE | Prototype + UI v2 done; 9 factory presets authored — tuning next |
| ScioEQP | ACTIVE | Prototype + UI v2 done; LF selector reconciled — 12AU7 param reconcile open |
| ScioMB | ACTIVE | v1 + v2 + post-v2 fixes all committed (6b037c6); calibration + host tuning next |

### Blocked

| Project | Blocker |
|---------|---------|
| ScioSound LMS | Webhook at /api/lemon-webhook non-functional until Netlify env vars set (part of Phase 14) |
| ScioEQP | 12AU7 param mismatch — use EQP-notes values {17.0,1.35,4200,84,300} |
| ScioSpace | Mono-safe threshold (corr > 0.7) provisional; IR library licensing unresolved |
| Patreon Integration | LMS Phase 12 must complete first; Patreon Creator account needed |

### Open Todos

| Project | Open |
|---------|------|
| ScioSound LMS | 3 |
| Scionaugh Website | 1 |
| Mixing Suite | 4 |
| ScioGlue | 4 |
| Sciotape | 2 |
| Sciotube | 1 |
| ScioSpace | 2 |
| ScioEQ | 6 |
| ScioEQP | 7 |
| ScioMB | 9 |
| Patreon Integration | 4 |

### Notes
- **LMS launch path cleared today:** Lemon Squeezy approved (2026-06-14) + DNS already on Netlify (verified 2026-06-15, NS1 nameservers + Netlify SOA). No MX records exist yet, so email is the one piece still to set up — but that's adding records, not a migration.
- Mixing Suite: ScioMB carries the largest open-todo load (9); calibration + host verification is the work front now that v2 + post-v2 fixes are committed.
- No HIGH priority projects set; all treated as MEDIUM.
