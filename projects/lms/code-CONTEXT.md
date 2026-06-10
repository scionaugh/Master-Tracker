# ScioSound LMS — Code Context

## Architecture Overview

Vanilla HTML/CSS/JS frontend. Supabase backend (Auth + PostgreSQL + Storage). Netlify hosting with serverless functions. No build step — publish directory is repo root.

```
sciosound-lms/
├── members/
│   ├── login.html       ← Email + password sign in
│   ├── signup.html      ← Account creation
│   ├── dashboard.html   ← Module map, progress, Continue CTA, upgrade banner
│   ├── module.html      ← Dynamic module page (loaded via ?id= param)
│   └── resources.html   ← Glossary (client-side search), PDFs, templates
├── js/
│   ├── supabase-client.js  ← Supabase init — single ESM import point (via esm.sh)
│   ├── auth.js             ← signIn/Up/Out, session guard, requireAuth, requireTier
│   ├── progress.js         ← getUserProgress, getNextModule, markModuleComplete, isModuleAccessible
│   ├── module-loader.js    ← Auth→tier→sequence gate, fetch, RENDERERS map, IntersectionObserver dots
│   └── submission.js       ← getSubmission, submitActivity (text-only, resubmission supported)
├── css/
│   └── members.css         ← Full design system: card states, block styles, stage colours
└── netlify/
    └── functions/
        └── lemon-webhook.js ← HMAC-SHA256 sig verify, upgrade + downgrade, user_id or email match
```

## Key Technical Decisions

**Auth:** Supabase email + password. Route guard in auth.js on every members/ page — no session redirects to login, wrong tier redirects with upgrade notice.

**Module content:** Polymorphic `content_blocks` table. `block_type` determines shape of `content` JSONB. 7 block types: headspace, text, video, activity, nerd, table, image. `interactive` deferred to Phase 13.

**RLS:** PostgreSQL row-level security on all tables. `supabase/08_grants.sql` required — RLS alone is not enough, explicit GRANT to authenticated role needed.

**body_html in text/nerd blocks rendered raw** (`innerHTML`) — trusted author content; INSERT blocked by RLS so no user-generated HTML.

**Webhook:** `netlify/functions/lemon-webhook.js` — dependency-free (node:crypto + global fetch). Handles upgrade and downgrade events. Redirected via `netlify.toml` to `/api/lemon-webhook`.

**Progress dots:** IntersectionObserver in module-loader.js. Completion UI revealed via fadeUp animation after final block. Progress bar animates via double rAF.

**Phase build order and models used:**

| Phase | Work | Status |
|-------|------|--------|
| 1 | Design system + HTML shells | ✓ Complete |
| 2 | Supabase schema + seed data | ✓ Complete |
| 3 | Auth implementation | ✓ Complete |
| 4 | Dashboard UI | ✓ Complete |
| 5 | Dashboard data layer | ✓ Complete |
| 6 | Module page shell | ✓ Complete |
| 7 | Content block engine | ✓ Complete |
| 8 | Activity + submission | ✓ Complete |
| 9 | Resources library | ✓ Complete |
| 10 | Payment webhook | ✓ Complete |
| 11 | Polish + responsive | ✓ Complete |
| 12 | Security hardening | ⬜ Next |
| New | sciosound.com/store.html | ⬜ Pre-launch |
| 13* | Interactive apps | ⬜ Post-launch |

## Dependencies on Other Projects
- **scionaugh.com (website):** This project must build sciosound.com/store.html before the website's store.html rework can happen
- **Mixing Suite:** Plugin products (ScioTube, ScioTape, ScioGlue, ScioSpace) sold via this store

## Known Issues / Technical Debt
- Email confirmation disabled in Supabase Auth (disabled during Phase 3 dev) — MUST re-enable before launch
- `supabase/*.sql` files are publicly reachable via Netlify (schema + anon key visible) — Phase 12 review item: deny or move to subfolder
- Netlify env vars not yet set (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, LEMON_SQUEEZY_SECRET) — payment webhook non-functional until set
- Real PDFs and templates not yet uploaded to Supabase Storage resources bucket

## Open Questions
- Phase 12 XSS audit: `body_html` trusted-author assumption — is this acceptable for launch?
- sciosound.com/store.html scope: combined course + plugins storefront, or separate pages?
- Price points for course subscriptions not yet decided
