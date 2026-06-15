# patreon-integration — Chat Context

## Goals and Purpose

Give Patreon subscribers access to sciosound.com content (course, plugins, or both) based on their tier, without requiring them to buy via Lemon Squeezy. Patreon is a parallel payment path — both Patreon and LS feed the same Supabase `membership_tier` field, so all gating logic stays unchanged.

Also introduces a monthly plugin choice mechanic: plugin-tier patrons pick one plugin per month from those they don't yet own. Accumulates until they've collected the full suite.

**Two-site role:**
- scionaugh.com — add Patreon CTAs to store.html and index.html
- sciosound.com — all OAuth, webhook, download, and picker logic lives here

## Key Decisions Made

**Supabase is the single source of truth.** Patreon does not talk to Lemon Squeezy directly. Both payment paths write to `users.membership_tier`. All existing tier-gating logic on sciosound.com works unchanged.

**Tier mapping:**
| Patreon tier | membership_tier |
|---|---|
| Course | `course` |
| Plugins | `plugins` |
| Bundle | `bundle` |

**Plugin delivery: Supabase-native (Phase 1 decision).** Plugin downloads served as Supabase Storage signed URLs, checked against `user_plugins`. No Lemon Squeezy license keys for Patreon users. Can migrate to LS license key API later if unified license management is needed.

**Monthly plugin pick mechanic:** Monthly cron flips `plugin_pick_available = true` for `plugins` and `bundle` tier users. Picker UI on sciosound.com dashboard shows only un-owned plugins. On pick: writes to `user_plugins`, logs to `plugin_picks`, flips flag back to false.

**What-if all plugins collected:** Not yet decided — options are banking picks, choosing extras (duplicates for gifting?), or pausing the picker. See TODO.

**Patreon integration was deferred from LMS Phase 14.** Now planned as its own project, starting after LMS pre-launch work (Phase 12 + store.html) is complete.

## Constraints and Dependencies

- **LMS project:** Phases 12 and store.html must be complete before Patreon work starts. Auth, tier-gating, and webhook patterns are already established there — Patreon work extends them.
- **Lemon Squeezy:** Account APPROVED 2026-06-14 — not a blocker for Patreon phases anyway; plugin downloads still need a Supabase storage bucket set up.
- **Patreon Creator account:** Must have tiers defined and webhook endpoint URL ready before Phase 3 can be completed end-to-end.
- **Plugin binaries:** Must be in Supabase Storage before Phase 6 can go live.

## Open Questions

- What are the Patreon tier names (public-facing) and exact prices?
- What happens when a patron has claimed all available plugins?
- Should plugin-tier patrons also get course preview access, or is course strictly a separate tier?
- Will Patreon bundle-tier patrons ever need LS license keys (e.g. for in-plugin license validation)?
