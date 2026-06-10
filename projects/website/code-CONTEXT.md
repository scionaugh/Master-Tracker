# Scionaugh Website — Code Context

## Architecture Overview
Static HTML/CSS/JS site. No build step, no framework. Publish directory is repo root (`.`). Deploys from `scionaugh/scionaugh-website` GitHub repo via Netlify on every push to main.

```
scionaugh-site-2/
├── index.html        ← Artist hub (hero, bio, performances, releases, listen, booking)
├── education.html    ← Course marketing (overview, curriculum, enroll CTA)
├── store.html        ← Plugin advertisement — 4 plugin listings, CTAs to sciosound.com/store
├── consent.html      ← Purchase consent (standalone — being moved to sciosound.com)
├── style.css         ← Shared design system across all pages
├── script.js         ← Toggle logic, drag scroll, parallax, player, reveal animations
└── images/           ← hero/, portraits/, logos/, releases/, performances/, plugins/
```

## Key Technical Decisions

**Dual-project toggle system:** `setProject(name)` in script.js controls `--project-accent` CSS variable and all identity-dependent elements. No separate pages per identity.

**SVG logo fix:** Logos use `fill="currentColor"` but loaded as `<img>` tags (can't inherit CSS color). Fixed with `filter: brightness(0) invert(1)` in style.css.

**store.html design system:** Separate dark amber design tokens defined in a `<style>` block inside store.html (not in style.css) — `--plug-bg`, `--plug-panel`, `--gold`, `--cream`, JetBrains Mono.

**Image rule:** Never generate or fabricate images. Create the folder if needed and tell the user which folder to drop files into.

## Dependencies on Other Projects
- **sciosound.com (LMS project):** store.html rework waits on sciosound.com/store page being built
- **Mixing Suite:** plugin UI screenshots (ScioTape.png, ScioTube.png, ScioGlue.png, ScioSpace.png) in images/plugins/

## Known Issues / Technical Debt
- Booking form not yet wired (Formspree needed)
- consent.html checkout URL is a placeholder — real Lemon Squeezy URL pending
- consent.html contact email missing
- og-image.jpg (1200x630 for social meta) not yet created
- Patreon link is a placeholder
- Bandcamp release card links not yet updated with real URLs
- `git config --global user.name/email` not yet set on this machine

## Open Questions
- Keep or remove consent.html once sciosound.com takes over the purchase flow?
- Mailing list platform for education.html signup field?
