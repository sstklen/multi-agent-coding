# Basic CLAUDE.md — Single Agent

> Use this as a starting point when you only have Claude Code.
> Add Codex and Gemini sections later when you hit their specific use cases.

```markdown
# Project: My API

## Stack
- Runtime: Bun
- Framework: Hono
- Database: SQLite (via Bun)
- Deployment: Docker on VPS

## Rules
- Always write tests before implementation
- Use TypeScript strict mode
- Never hardcode API keys — read from database
- Commit messages: imperative mood, 1-2 sentences

## Architecture
src/
├── routes/      # API endpoints
├── middleware/   # Auth, rate-limit, error handling
├── services/    # Business logic
├── db/          # Database queries
└── utils/       # Shared helpers

## Verification
After writing code, always:
1. Run `bun test` to verify
2. Check for TypeScript errors: `bun run typecheck`
3. Test the endpoint manually with curl
```

## Why This Works

Claude Code reads `CLAUDE.md` at the start of every session. By putting your project context, rules, and architecture here, you eliminate the "cold start" problem where Claude asks 10 questions before writing a single line.

## Key Principles

1. **Stack first** — Claude needs to know what tools to use
2. **Rules are constraints** — they prevent common mistakes before they happen
3. **Architecture is a map** — Claude makes better decisions when it knows where things go
4. **Verification is mandatory** — without it, Claude writes code that "looks right" but breaks
