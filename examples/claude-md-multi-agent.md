# Multi-Agent CLAUDE.md — 3 Agent Team

> This is the actual configuration we use at Washin Village.
> Claude Code reads this and knows when to dispatch to Codex or Gemini.

```markdown
# Project: Washin API Marketplace

## Stack
- Runtime: Bun
- Framework: Hono
- Database: SQLite (via Bun)
- Reverse proxy: Caddy (auto-HTTPS)
- Deployment: Docker on VPS (8GB RAM)

## Agent Dispatch Rules

| Task | Agent | Command |
|------|-------|---------|
| Images/PDFs/videos | Gemini | `gemini -m gemini-2.5-flash -p "..."` |
| Web search, latest info | Gemini | `gemini -m gemini-2.5-flash -p "search: ..."` |
| Large codebase analysis (>1M tokens) | Gemini | `gemini -m gemini-2.5-flash -p "analyze: ..."` |
| Bulk refactoring 10+ files | Codex | `codex exec "..." --full-auto` |
| Long background tests | Codex | `codex exec "bun test" --full-auto` |
| Batch rename/move files | Codex | `codex exec "..." --full-auto` |
| Architecture decisions | Self (Claude Code) | Direct |
| Complex debugging | Self (Claude Code) | Direct |
| Everything else | Self (Claude Code) | Direct |

## Verification by Risk Level
- Low (formatting, README): use directly
- Medium (Codex refactoring, Gemini research): spot-check
- High (money, database, deployment): full verification

## Three-Decision Framework
1. Can I decide this myself? → Just do it
2. Is there risk? → Give 2-3 options, let human pick
3. Does it touch money/DB/deploy? → Must ask first

## Gemini Hallucination Rule
The more confident Gemini sounds, the more you should verify.
Gemini will say "I've fixed everything!" when it hasn't found the right file.
Always ask for specific file paths and line numbers.
```

## What Makes This Different

The key innovation is that **Claude Code decides which agent to use, not the human.** The human says "fix the login bug and update the README." Claude Code decides:
- Login bug = complex reasoning → handle myself
- README update = mechanical → dispatch to Codex

This eliminates the cognitive overhead of the human having to think "which tool should I use?"

## Usage

1. Copy this to your project's `CLAUDE.md`
2. Adjust the stack section to match your project
3. Install Codex CLI: `npm install -g @openai/codex`
4. Install Gemini CLI: `npm install -g @google/gemini-cli`
5. Claude Code will automatically follow these dispatch rules
