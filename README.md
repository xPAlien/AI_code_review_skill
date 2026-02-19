# AI Code Review Skill

A systematic code review checklist for AI-generated code. Works with Claude.ai, Claude Code, and any tool supporting the [Agent Skills](https://agentskills.io) open standard.

AI-generated code optimizes for the happy path. It compiles. Tests pass. But it routinely skips debouncing, input validation, error handling, connection management, logging, and security hardening. This skill enforces a structured review that catches those gaps before code reaches production.

## What This Skill Does

When you ask Claude to review code (or it detects AI-generated code), this skill triggers a 4-step process:

1. **Comprehension Gate** — Can you explain what the code does and why it fails?
2. **35-Item Checklist** — Evaluates security, performance, error handling, observability, architecture fit, and code quality
3. **Review Report** — Structured output with PASS/FAIL per item and concrete fix recommendations
4. **Severity Classification** — CRITICAL, HIGH, MEDIUM, LOW with merge/block guidance

## What It Catches

The checklist covers 7 domains:

- Comprehension and intent (dead code, unclear naming, missing mental model)
- Edge cases and error handling (null values, timeouts, race conditions, partial failures)
- Security (SQL injection, missing auth/authz, hardcoded secrets, permissive CORS)
- Performance and scalability (no debounce, N+1 queries, missing indexes, no connection pooling)
- Observability (no structured logging, swallowed errors, no health checks)
- Architecture fit (pattern violations, wrong sync/async choice, unjustified dependencies)
- Code quality (untested unhappy paths, tautological tests, duplicated logic)

The `references/common-failures.md` file documents the 10 patterns AI breaks most often with bad/good code examples.

---

## Installation

### Claude.ai (Web / Mobile / Desktop)

1. Download or clone this repo
2. Zip the `ai-code-review` folder (the folder itself must be the root of the ZIP)
3. Go to **Settings > Capabilities > Skills**
4. Click **Upload skill**
5. Select the ZIP file
6. Toggle the skill on

The ZIP structure should look like this:

```
ai-code-review.zip
└── ai-code-review/
    ├── SKILL.md
    └── references/
        ├── checklist.md
        └── common-failures.md
```

### Claude Code (CLI)

Copy the skill folder into your Claude Code skills directory:

```bash
# User-level (available in all projects)
cp -r ai-code-review ~/.claude/skills/

# Project-level (available only in this repo)
cp -r ai-code-review .claude/skills/
```

Claude Code will auto-discover the skill. No restart needed.

### Any Agent Skills Compatible Tool

This skill follows the [Agent Skills](https://agentskills.io) open standard. Place the `ai-code-review` folder wherever your tool reads skills from. The `SKILL.md` frontmatter handles discovery.

---

## File Structure

```
ai-code-review/
├── SKILL.md                         # Entry point. Trigger conditions, workflow, output format.
└── references/
    ├── checklist.md                  # 35 review items across 7 domains with explanations.
    └── common-failures.md           # 10 most frequent AI code failures with code examples.
```

## Usage

Once installed, the skill triggers when you:

- Ask Claude to "review this code" or "check my code"
- Paste code and ask "is this safe" or "audit this"
- Mention vibe coding, shipping AI code, or debugging code you did not write
- Request a pre-merge review

You do not need to invoke the skill manually. Claude loads it automatically when the context matches.

### Example Prompts

```
Review this Express API endpoint for production readiness.
```

```
I used Copilot to write this database query. Is it safe to ship?
```

```
Audit this React component before I merge it.
```

## Contributing

PRs welcome. If you find a pattern AI consistently gets wrong that is not covered by the checklist, open an issue or submit a PR adding it to `references/common-failures.md`.

To add a new checklist item:

1. Add a row to the relevant domain table in `references/checklist.md`
2. Follow the format: item number, check description, explanation of why it matters
3. Keep the explanation to one sentence focused on the production consequence

## License

MIT License. See [LICENSE](LICENSE).
