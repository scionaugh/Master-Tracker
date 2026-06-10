# Instructions for Claude

## Daily Check-In Protocol

- **Always** read all `STATUS.md` files before responding to any daily check-in
- **Only** read `chat-CONTEXT.md` and `code-CONTEXT.md` when asked for deeper evaluation or cross-project analysis
- Each project has a **single STATUS.md** — this is the one source of truth regardless of whether work happened in chat or code

## Daily Dashboard

When asked for a daily dashboard:
- Summarise all projects from `projects/*/STATUS.md` into a single clean view
- Flag anything marked as `BLOCKED`

## File Update Rule

**Never update any file unless explicitly asked to.**

## Folder Reference

```
master-tracker/
├── DASHBOARD.md
├── INSTRUCTIONS.md
└── projects/
    └── [project-name]/
        ├── STATUS.md          ← single source of truth for current state
        ├── chat-CONTEXT.md    ← planning/conversation background (read on request)
        └── code-CONTEXT.md    ← technical background (read on request)
```

## Notes

- Projects may have one or both CONTEXT files depending on whether they have a chat and/or coding component
- If a project is chat-only, `code-CONTEXT.md` can be omitted (and vice versa)

## Version Planning — Context Update Rule

When reading CONTEXT files during a daily check-in or evaluation, if any file contains the line `CONTEXT UPDATE NEEDED`, flag it prominently in the dashboard like this:

> ⚠️ **[project-name]** — Context update needed for version X. Run the new-version prompts in the relevant chats, then ask me to update the files.

Do not clear the `CONTEXT UPDATE NEEDED` flag automatically. Only remove it when explicitly asked to update that CONTEXT file with new content.

## Auto-Update Notes

- STATUS.md files for the Mixing Suite are auto-updated on every git commit via `scripts/update-tracker.sh`
- To start version planning: run `./scripts/new-version.sh <version>` from the Mixing Suite project root
- CONTEXT files are never auto-updated — they require a deliberate update prompt run in the relevant chat

## Priority Field

Each STATUS.md has a `Priority` field: HIGH / MEDIUM / LOW.

When generating a daily dashboard:
- Lead with HIGH priority projects
- Include MEDIUM projects in the main view
- Only show LOW priority projects if explicitly asked or if they have a BLOCKER

When a project has no priority set, treat it as MEDIUM.

## Daily Log

Every time a daily dashboard or project overview is generated:

1. Write the full dashboard output to `daily-logs/YYYY-MM-DD.md` (use today's date)
2. If a log for today already exists, append to it with a timestamp rather than overwriting
3. Also overwrite `DASHBOARD.md` with the latest snapshot for quick reference

### Log file format

```markdown
# YYYY-MM-DD

## [time or check-in number if multiple in one day]

### Projects
[dashboard content]

### Blocked
[blocked items]

### Notes
[anything flagged or noteworthy from this check-in]
```

This creates a searchable history of project state over time — useful for spotting stale blockers, tracking velocity, and reviewing progress across weeks.
