# Agent Dispatch Rules — Deep Dive

## The Core Principle

> The Boss (Claude Code) decides who does the work. Not the human.

This is counterintuitive. Most people think: "I'll use Codex for this, Gemini for that." But that creates cognitive overhead — the human becomes the bottleneck, spending time deciding which tool to use instead of deciding *what to build*.

The better pattern: tell Claude Code what you want, and let it dispatch.

---

## Decision Matrix

### When does the Boss dispatch to Codex?

Codex CLI excels at **mechanical, parallelizable work**:

```
✅ Good Codex tasks:
- Rename a variable across 20 files
- Add error handling to all API routes
- Convert callback-style code to async/await
- Run test suites that take 10+ minutes
- Apply a consistent code pattern across many files

❌ Bad Codex tasks:
- "Fix the authentication bug" (needs reasoning)
- "Design the new billing system" (needs judgment)
- "Should we use Redis or SQLite?" (needs context)
```

**Dispatch command:**
```bash
codex exec "Refactor all files in src/routes/ to use the new errorHandler wrapper. Pattern: replace try/catch blocks with handleError(). Do not change any business logic." --full-auto
```

Key: be **extremely specific** about what to change and what NOT to change.

### When does the Boss dispatch to Gemini?

Gemini CLI excels at **perception and research**:

```
✅ Good Gemini tasks:
- "What does this screenshot show?" (vision)
- "Summarize this 50-page PDF" (document analysis)
- "What's the current pricing for DeepL API?" (web search)
- "Analyze this 500K-line codebase for patterns" (large context)

❌ Bad Gemini tasks:
- "Write the authentication middleware" (will hallucinate)
- "Fix this bug in line 42" (will claim success falsely)
- "Refactor this module" (unreliable code output)
```

**Dispatch command:**
```bash
gemini -m gemini-2.5-flash -p "Search for the current pricing tiers of DeepL API, Google Translate API, and AWS Translate. Return as a comparison table with: free tier limits, price per character, and supported languages count."
```

Key: ask for **specific output format** so the Boss can immediately use the result.

### When does the Boss handle it directly?

Claude Code handles everything that requires **reasoning, judgment, or multi-step planning**:

```
✅ Boss tasks:
- Architecture decisions
- Complex debugging
- Writing new features from specifications
- Code review and security analysis
- Anything that touches money, auth, or databases
- Orchestrating multi-step workflows
```

---

## Real-World Dispatch Examples

### Example 1: New API Endpoint

```
Human: "Add a translation endpoint that supports 5 providers with fallback"

Boss decides:
  1. Design architecture (provider interface, fallback chain) → SELF
  2. Research current pricing for each provider → GEMINI
  3. Write main endpoint + interface → SELF
  4. Implement 5 provider adapters (mechanical) → CODEX
  5. Integrate everything + write tests → SELF

Result: 25 minutes instead of 2-3 hours
```

### Example 2: Security Audit

```
Human: "Do a security audit of the API"

Boss decides:
  1. Review auth middleware logic → SELF
  2. Check all routes for missing auth → SELF
  3. Scan for hardcoded secrets across all files → CODEX
  4. Research latest OWASP top 10 → GEMINI
  5. Write audit report → SELF

Result: Comprehensive audit in 40 minutes
```

### Example 3: Bug Investigation

```
Human: "Users report 502 errors intermittently"

Boss decides:
  1. Read error logs → SELF
  2. Analyze timeout chain (CDN → Caddy → App) → SELF
  3. Check if similar issues reported online → GEMINI
  4. Fix the root cause → SELF
  5. Add monitoring for the specific error pattern → SELF

Note: This is almost entirely Boss work.
Not everything needs multiple agents.
```

---

## The 80/20 Rule

After 6 months of multi-agent development:

- **Claude Code handles 80%** of all work
- **Codex handles 15%** (bulk mechanical tasks)
- **Gemini handles 5%** (vision + research)

Don't over-engineer the orchestration. Most tasks are best handled by one smart agent.
