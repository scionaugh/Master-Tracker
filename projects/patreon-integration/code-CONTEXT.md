# patreon-integration — Code Context

## Architecture Overview

Extends the existing sciosound-lms stack (Vanilla HTML/CSS/JS, Supabase, Netlify functions). No new infrastructure — new Supabase tables, two new Netlify functions, and new UI pages/components on sciosound.com.

```
sciosound-lms/
├── members/
│   ├── dashboard.html          ← Add plugin picker card + "Connect Patreon" prompt
│   ├── plugins.html            ← New: download hub, owned plugins, picker UI
│   └── account.html            ← Add "Connect Patreon" OAuth button + linked status
├── js/
│   ├── auth.js                 ← Extend requireTier() for 'course' | 'plugins' | 'bundle'
│   ├── patreon-auth.js         ← New: OAuth flow, token exchange, patreon_id storage
│   └── plugin-picker.js        ← New: picker UI logic, eligibility check, pick submission
└── netlify/
    └── functions/
        ├── patreon-oauth.js    ← New: exchanges Patreon OAuth code for token, stores patreon_id
        ├── patreon-webhook.js  ← New: validates Patreon webhook sig, syncs membership_tier
        └── plugin-cron.js      ← New: monthly job — flips plugin_pick_available for eligible users

supabase/
├── 09_patreon_schema.sql       ← New columns + tables (see Phase 1)
└── 10_patreon_rls.sql          ← RLS policies for new tables
```

## Supabase Schema Changes

```sql
-- On existing users table
ALTER TABLE users ADD COLUMN patreon_id TEXT UNIQUE;
ALTER TABLE users ADD COLUMN patreon_tier TEXT;
ALTER TABLE users ADD COLUMN plugin_pick_available BOOLEAN DEFAULT FALSE;

-- New table: which plugins each user owns
CREATE TABLE user_plugins (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  plugin_slug TEXT NOT NULL,  -- 'sciotube' | 'sciotape' | 'scioglue' | 'sciospace'
  granted_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, plugin_slug)
);

-- New table: audit log of monthly picks
CREATE TABLE plugin_picks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  month TEXT NOT NULL,        -- 'YYYY-MM'
  plugin_slug TEXT NOT NULL,
  picked_at TIMESTAMPTZ DEFAULT NOW()
);
```

## Phase Build Order

| Phase | Work | Scope | Status |
|-------|------|-------|--------|
| 1 | Supabase schema — new columns + tables + RLS | sciosound-lms | ⬜ |
| 2 | Patreon OAuth connect flow | sciosound-lms | ⬜ |
| 3 | Patreon webhook handler | sciosound-lms | ⬜ |
| 4 | Tier-gating updates — extend requireTier() + dashboard | sciosound-lms | ⬜ |
| 5 | Monthly cron + plugin picker UI | sciosound-lms | ⬜ |
| 6 | Plugin download delivery — signed Supabase Storage URLs | sciosound-lms | ⬜ |
| 7 | scionaugh.com Patreon CTAs | scionaugh-website | ⬜ |

### Phase Detail

**Phase 1 — Schema**
`09_patreon_schema.sql`: add `patreon_id`, `patreon_tier`, `plugin_pick_available` to users. Create `user_plugins` and `plugin_picks` tables. `10_patreon_rls.sql`: RLS so users can only read their own rows.

**Phase 2 — Patreon OAuth connect**
Account page: "Connect Patreon" button. Redirects to Patreon OAuth. `patreon-oauth.js` (Netlify function) exchanges code for token, fetches identity + tier from Patreon API, writes `patreon_id` and sets `membership_tier` in Supabase. Show connected status on account page.

**Phase 3 — Patreon webhook handler**
`patreon-webhook.js`: validate webhook secret (HMAC), handle `pledges:create`, `pledges:update`, `pledges:delete`. Map Patreon tier amount → `membership_tier`. On delete/cancel: downgrade tier. Mirror pattern of `lemon-webhook.js`.

**Phase 4 — Tier-gating updates**
Extend `auth.js` `requireTier()` to handle `course`, `plugins`, `bundle` (bundle satisfies both). Update dashboard: show course modules only if `course` or `bundle`. Show plugin section only if `plugins` or `bundle`. Existing `paid` tier from LS treated as `bundle` for backwards compatibility.

**Phase 5 — Monthly cron + picker UI**
`plugin-cron.js` (Netlify scheduled function or external cron): runs 1st of each month, sets `plugin_pick_available = true` for users with `membership_tier` in `['plugins','bundle']`. `plugins.html`: picker card shows when `plugin_pick_available = true`, lists un-owned plugins. On pick: POST to edge function → insert `user_plugins`, insert `plugin_picks`, flip flag false.

**Phase 6 — Plugin download delivery**
Supabase Storage bucket `plugin-binaries` (private). On dashboard/plugins.html: for each owned plugin, generate signed URL (short TTL, e.g. 1 hour) server-side via Netlify function. Serve as download button. No direct public URLs.

**Phase 7 — scionaugh.com CTAs**
`store.html`: add Patreon tier section alongside direct-buy cards. `index.html`: optional support/subscribe section. Link to Patreon page. No functional changes to scionaugh.com — marketing only.

## Key Technical Notes

- Patreon webhook secret stored as `PATREON_WEBHOOK_SECRET` Netlify env var (alongside existing `LEMON_SQUEEZY_SECRET`)
- Patreon Client ID + Secret stored as `PATREON_CLIENT_ID`, `PATREON_CLIENT_SECRET`
- OAuth redirect URI: `https://sciosound.com/api/patreon-oauth` (via netlify.toml redirect)
- Patreon tier → membership_tier mapping defined as a constant in `patreon-webhook.js` — update here when tiers change
- `plugin_pick_available` is reset to false on pick; cron sets it true again next month
- Signed URL generation must happen server-side (service role key) — never expose storage bucket publicly

## Dependencies on Other Projects

- **sciosound-lms:** All phases 1–6 live in this repo
- **scionaugh-website:** Phase 7 only — marketing CTAs, no functional changes
- **Mixing Suite:** Plugin binary files (.dmg / .exe / .pkg) must be uploaded to Supabase Storage before Phase 6 goes live

## Known Issues / Technical Debt
- `membership_tier` currently uses 'paid' / 'free' — Phase 4 must handle backwards compatibility
- Plugin binaries not yet code-signed (Apple/Windows) — download delivery can be built but not publicly launched until signing is done
