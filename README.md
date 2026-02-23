<p align="center">
  <h1 align="center">Multi-Agent Coding</h1>
  <p align="center"><strong>How we use 3 AI agents as a full engineering team.</strong></p>
  <p align="center">Claude Code as the boss. Codex + Gemini as the team. Real patterns from production.</p>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Agents-Claude_+_Codex_+_Gemini-blue?style=for-the-badge" alt="3 Agents"/>
  <img src="https://img.shields.io/badge/Result-30+_APIs_Shipped-green?style=for-the-badge" alt="30+ APIs"/>
  <img src="https://img.shields.io/badge/Engineers-Zero-red?style=for-the-badge" alt="Zero Engineers"/>
</p>

<p align="center">
  <a href="https://github.com/sstklen/multi-agent-coding/stargazers"><img src="https://img.shields.io/github/stars/sstklen/multi-agent-coding?style=social" alt="Stars"/></a>
</p>

---

## The Setup

We run an animal sanctuary in Japan. 28 cats and dogs. No engineering team.

We built an [API marketplace](https://api.washinmura.jp) with 30+ integrations, token economy, multi-language support, Docker deployment — using **3 AI coding agents working as a team.**

This document is exactly how we orchestrate them.

---

## The Three Agents

```
┌─────────────────────────────────────────────────┐
│                 Claude Code (Boss)               │
│  "The Old Man" — thinks, plans, decides, codes  │
│  Model: Claude Sonnet/Opus                       │
└──────────┬──────────────────┬────────────────────┘
           │                  │
     ┌─────▼──────┐    ┌─────▼──────┐
     │   Codex    │    │   Gemini   │
     │ "Muscle"   │    │  "Eyes"    │
     │ Bulk work  │    │ Vision +   │
     │ Background │    │ Research   │
     └────────────┘    └────────────┘
```

| Agent | Codename | Best at | Weak at |
|-------|----------|---------|---------|
| **Claude Code** | The Boss | Planning, architecture, complex logic, multi-file edits | Can't see images, limited web search |
| **Codex CLI** | Muscle | Bulk refactoring 10+ files, long background jobs, parallel tests | No vision, can't browse web |
| **Gemini CLI** | Eyes | Reading images/PDFs/videos, web search, analyzing 1M+ token codebases | High hallucination rate, overclaims solutions |

---

## The Dispatch Table

When a task comes in, the Boss decides who handles it:

| Task | Who | Why |
|------|-----|-----|
| **Read screenshot / PDF / video** | Gemini | Only agent with vision |
| **"What's trending in AI this week?"** | Gemini | Real-time web search |
| **Analyze a 500K-line codebase** | Gemini | Handles massive context windows |
| **Refactor 10+ files** | Codex `--full-auto` | Bulk parallel work, won't get distracted |
| **Run test suite for 30 minutes** | Codex (background) | Long-running, no interaction needed |
| **Batch rename across project** | Codex `--full-auto` | Mechanical, parallelizable |
| **Design new feature architecture** | Boss (Claude) | Needs judgment and planning |
| **Debug a subtle logic bug** | Boss (Claude) | Needs deep reasoning |
| **Write API endpoint + tests** | Boss (Claude) | Multi-step, needs context |
| **Scheduled monitoring** | Nebula (cron) | Runs on its own schedule |
| **Cross-platform integration** | Nebula | Bridges external services |
| **Everything else** | Boss (Claude) | Default — handles 80% of work |

---

## The Rules

### Rule 1: Boss decides, never the human

The human says *what* they want. The Boss decides *who* does it. The human never needs to think about which agent to use.

```
Human:  "Fix the login bug and update the README"
Boss:   (thinks) Login bug = complex reasoning → I'll do it
        (thinks) README update = mechanical → dispatch to Codex
```

### Rule 2: Trust but verify (by risk level)

| Risk | Action | Example |
|------|--------|---------|
| 🟢 Low | Use directly | Code formatting, README updates |
| 🟡 Medium | Spot-check | Codex refactoring, Gemini research |
| 🔴 High | Full verification | Anything touching money, database, or deployment |

> **Gemini hallucination rule:** The more confident Gemini sounds, the more you should verify. Gemini will say "I've fixed everything!" when it hasn't even found the right file.

### Rule 3: Three-decision framework

The Boss follows this decision tree for every action:

```
Can I decide this myself?     → Just do it (rename a variable, fix a typo)
Is there risk?                → Give options (2-3 approaches, let human pick)
Does it touch money/DB/deploy? → Must ask (never proceed without confirmation)
```

### Rule 4: Cross-agent communication

When the Boss dispatches work, the output is structured so the Boss can immediately use it:

```
Dispatch to Codex:
  "Refactor all API routes to use the new error handler.
   Files: src/routes/*.ts
   Pattern: replace try/catch with handleError wrapper
   Expected: ~15 files changed, no logic changes"

Codex returns:
  "Done. 14 files changed. 1 file skipped (already used handleError).
   Files: [list]
   Tests: all passing"

Boss verifies → merges
```

---

## Parallel Development Pattern

The most powerful pattern: **running 3 Claude Code terminals simultaneously.**

```
Terminal 1 (Boss):     Feature development
Terminal 2 (Worktree): Bug fixes on separate branch
Terminal 3 (Review):   Code review + testing
```

### How it works

```bash
# Terminal 1: Main development
claude code  # Working on feature branch

# Terminal 2: Isolated bug fix (separate git worktree)
# Claude Code creates a worktree automatically
# Changes don't conflict with Terminal 1

# Terminal 3: QA and testing
claude code  # Run tests, review code from other terminals
```

### Communication between terminals

The human acts as message router:

```
Terminal 1: "I've finished the API endpoint. Tell the reviewer."
Human:      (copy-pastes summary to Terminal 3)
Terminal 3: "Reviewing... Found 2 issues. Tell the developer."
Human:      (copy-pastes issues to Terminal 1)
```

> **Tip:** Each terminal maintains its own context. Keep messages between them structured and concise — don't paste entire conversations.

---

## Real Example: Building an API Endpoint

Here's how a typical task flows through the system:

```
Human: "Add a new /api/translate endpoint that supports 5 providers"

Boss (Claude Code):
  1. Plans architecture (provider interface, fallback logic, pricing)
  2. Writes the main endpoint + provider interface
  3. Dispatches to Codex: "Implement 5 provider adapters using this interface"
  4. Dispatches to Gemini: "Research current pricing for DeepL, Google, AWS Translate"
  5. Integrates Codex's adapters + Gemini's pricing data
  6. Writes tests
  7. Commits

Time: 25 minutes (vs 2-3 hours solo coding)
```

---

## Setup

### Prerequisites

```bash
# Claude Code (the boss)
# Install: https://docs.anthropic.com/en/docs/claude-code

# Codex CLI (optional — for bulk work)
npm install -g @openai/codex

# Gemini CLI (optional — for vision + research)
npm install -g @google/gemini-cli
```

### Configuration

Add to your `CLAUDE.md` (project-level instructions):

```markdown
## Agent Dispatch Rules

| Task | Agent |
|------|-------|
| Images/PDFs/videos, web search, large codebase analysis | Gemini |
| Bulk refactoring 10+ files, background tests, mechanical tasks | Codex --full-auto |
| Scheduled monitoring, cross-platform integration | Nebula |
| Everything else | Claude Code (self) |

## Dispatch Commands
- Codex: `codex exec "..." --full-auto --skip-git-repo-check`
- Gemini: `gemini -m gemini-3-flash -p "..."`

## Verification by Risk
- 🟢 Low → use directly
- 🟡 Medium → spot-check
- 🔴 High (money/DB/deploy) → full verification
```

---

## Lessons Learned

After 6 months of multi-agent development:

### What works

- **Parallel terminals** — 3x throughput on independent tasks
- **Codex for bulk** — Renaming 20 files in 2 minutes instead of 20
- **Gemini for research** — Reading a 50-page PDF and summarizing in 30 seconds
- **Clear dispatch rules** — No time wasted deciding "which agent should do this?"

### What doesn't work

- **Gemini for code** — It hallucinates function signatures and claims success when it failed
- **Codex for architecture** — It follows instructions literally but can't make judgment calls
- **Too many agents** — 3 is the sweet spot. More than that = more coordination overhead than value
- **Trusting Gemini's confidence** — "I've fixed everything!" → nothing was fixed

### The 80/20 rule

In practice, Claude Code handles **80% of all work.** Codex handles 15% (bulk tasks). Gemini handles 5% (vision + research). Don't over-engineer the orchestration — most tasks are best handled by one smart agent, not a committee.

---

## Related Projects

| Project | Description |
|---------|-------------|
| [**112 Claude Code Skills**](https://github.com/sstklen/washin-claude-skills) | Everything we learned, extracted as reusable skills |
| [**AI API Benchmark**](https://github.com/sstklen/washin-api-benchmark) | Monthly tests of 30+ AI APIs from Tokyo |
| [**AI Prompt Mastery**](https://github.com/sstklen/ai-prompt-mastery) | One prompt to make any AI respond like an expert |

---

<p align="center">
  <sub>
    Built at <a href="https://washinmura.jp">Washin Village</a> — an animal sanctuary in Japan where 28 cats & dogs and 3 AI agents work together.
  </sub>
</p>
