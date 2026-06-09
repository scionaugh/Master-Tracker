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
