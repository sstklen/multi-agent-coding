# Parallel Development CLAUDE.md — 3 Terminals

> Run 3 Claude Code instances simultaneously for 3x throughput.
> Each terminal works on independent tasks without conflicts.

```markdown
# Terminal Assignment

## Terminal 1: Feature Development (main branch)
You are working on new feature development.
- Branch: feature/* branches
- Focus: writing new code, implementing features
- Do NOT touch files that Terminal 2 is working on

## Terminal 2: Bug Fix (worktree)
You are working on bug fixes in an isolated git worktree.
- The worktree is at: .claude/worktrees/bugfix/
- Focus: fixing bugs, hotfixes
- Changes here do NOT affect Terminal 1

## Terminal 3: QA & Review
You are reviewing code from Terminal 1 and Terminal 2.
- Focus: running tests, code review, security audit
- Do NOT make code changes — only report issues
- Use structured format for reports:
  [FILE] path/to/file
  [LINE] 42
  [ISSUE] Description of the problem
  [SEVERITY] HIGH/MEDIUM/LOW
```

## How Parallel Development Works

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ Terminal 1   │    │ Terminal 2   │    │ Terminal 3   │
│ Features     │    │ Bug Fixes    │    │ QA & Review  │
│              │    │ (worktree)   │    │              │
│ main branch  │    │ fix/ branch  │    │ read-only    │
└──────┬───────┘    └──────┬───────┘    └──────┬───────┘
       │                   │                   │
       └───────────────────┴───────────────────┘
                    Human = Router
       (copy-paste summaries between terminals)
```

## Communication Protocol

Terminals can't talk to each other directly. The human acts as a message router:

1. Terminal 1 finishes a feature → tells human "Feature X is done"
2. Human copies summary to Terminal 3 → "Review feature X in src/routes/translate.ts"
3. Terminal 3 reviews → reports issues to human
4. Human copies issues to Terminal 1 → "Fix these 2 issues"

**Keep messages between terminals structured and concise.** Don't paste entire conversations — just the actionable items.

## When to Use This

- Large feature + urgent bug fix simultaneously
- Feature development + comprehensive test suite
- Multiple independent features that don't touch the same files
- Code review happening in parallel with development

## When NOT to Use This

- Tasks that touch the same files (will cause merge conflicts)
- Simple tasks that one terminal can handle in 10 minutes
- When you're learning — start with one terminal first
