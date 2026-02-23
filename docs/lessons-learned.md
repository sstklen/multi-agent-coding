# Lessons Learned — 6 Months of Multi-Agent Development

## Lesson 1: Start with one agent

Don't start with 3 agents. Start with Claude Code alone. Use it for everything. Hit its limitations naturally.

Only then add Codex (when you have bulk mechanical work) and Gemini (when you need vision or web search).

Starting with 3 agents is like hiring a team before you understand the job.

## Lesson 2: Gemini lies confidently

Gemini CLI is amazing at vision and research. It can read PDFs, analyze screenshots, and search the web.

But it **hallucinates code**. It will:
- Claim it fixed a bug when it didn't even find the right file
- Generate function signatures that don't exist
- Say "I've verified this works" when it hasn't tested anything

**Rule:** Never let Gemini write production code. Use it for information gathering only.

## Lesson 3: Codex needs extreme specificity

Codex CLI follows instructions literally. This is a strength for mechanical tasks but a weakness for anything requiring judgment.

Bad dispatch:
```
"Improve the error handling in our API"
```

Good dispatch:
```
"In every file matching src/routes/*.ts, replace bare try/catch blocks
with the handleError() wrapper from src/utils/error-handler.ts.
Do not modify the catch logic — only wrap the existing try/catch.
Do not touch src/routes/health.ts."
```

## Lesson 4: Three terminals is the sweet spot

We tried running 4-5 Claude Code instances simultaneously. The coordination overhead exceeded the productivity gains. With 3 terminals:

- Terminal 1: Feature development
- Terminal 2: Bug fixes (git worktree for isolation)
- Terminal 3: Testing and review

The human routes messages between terminals. More than 3 means too many context switches for the human.

## Lesson 5: The human is the bottleneck, not the AI

AI agents can write code faster than you can review it. The limiting factor is always:
- How clearly you define what to build
- How quickly you verify the output
- How well you prioritize what matters

The goal of multi-agent orchestration isn't "more code faster" — it's "more parallel progress on independent tasks."

## Lesson 6: Verification by risk level saves time

Not everything needs full verification:

| Risk | Action | Time saved |
|------|--------|-----------|
| Low (formatting, docs) | Trust directly | 100% |
| Medium (refactoring) | Spot-check 2-3 files | 70% |
| High (money, auth, DB) | Full review + test | 0% (but worth it) |

The mistake most people make: treating everything as high-risk. This makes AI-assisted development slower than manual coding.

## Lesson 7: Skills compound over time

We extracted every bug fix into a reusable skill (112 total). After 6 months:

```
Month 1: Bug → 2-4 hours of debugging
Month 3: Bug → 30-60 minutes
Month 7: Bug → instant fix from skill library
```

The real power of multi-agent development isn't speed — it's **institutional memory**. Skills make the AI team smarter over time, like a real engineering team building tribal knowledge.
