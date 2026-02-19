---
name: ai-code-review
description: >
  Systematic code review checklist for AI-generated code. Use this skill whenever
  reviewing code produced by AI assistants (ChatGPT, Copilot, Claude, Cursor, etc.),
  or when the user asks to review, audit, or harden any code they did not write from
  scratch themselves. Also trigger when the user says "review this," "is this safe,"
  "check my code," "audit this," "harden this," or pastes code they want evaluated
  before merging or deploying. If the user mentions vibe coding, shipping AI code,
  or debugging code they do not fully understand, use this skill.
---

# AI Code Review Skill

## Purpose

AI-generated code optimizes for the happy path. It compiles. Tests pass. But it
routinely skips debouncing, input validation, error handling, connection management,
logging, and security hardening. This skill enforces a structured review process
that catches those gaps before code reaches production.

## When to Use

- Any pull request or code block generated (fully or partially) by an AI tool
- Code the developer copied from a chat session and wants to validate
- Pre-merge reviews where the author admits limited understanding of the code
- Post-incident reviews where AI-generated code is suspected as the root cause

## Review Workflow

Follow this sequence for every review. Do not skip steps.

### Step 1: Comprehension Gate

Before reviewing anything else, confirm the developer (or you, acting as reviewer)
can answer these three questions about the code:

1. What problem does this code solve?
2. Why was this approach chosen over alternatives?
3. What happens when this code fails?

If any answer is "I don't know," flag it. Code that nobody understands should not
ship. This is the single highest-priority check in the entire review.

### Step 2: Run the Checklist

Read `references/checklist.md` and evaluate every item against the code under review.
Mark each item PASS, FAIL, or N/A. Provide a one-line explanation for every FAIL.

The checklist covers seven domains:
1. Comprehension and intent
2. Edge cases and error handling
3. Security
4. Performance and scalability
5. Observability
6. Architecture fit
7. Code quality

### Step 3: Produce the Review Report

Output the review in this format:

```
REVIEW SUMMARY
==============
File(s): [filenames]
Source: [AI tool used, if known]
Verdict: PASS / CONDITIONAL PASS / FAIL

FAILURES
========
[domain] - [checklist item]: [one-line explanation]

WARNINGS
========
[items that passed but are borderline or worth monitoring]

RECOMMENDATIONS
===============
[concrete fixes, ordered by severity]
```

### Step 4: Severity Classification

Classify every failure:

- CRITICAL: Will cause data loss, security breach, or downtime. Block merge.
- HIGH: Will cause bugs under load or in edge cases. Block merge.
- MEDIUM: Maintainability or observability gap. Fix before next release.
- LOW: Style or convention issue. Fix when convenient.

Any CRITICAL or HIGH failure means the code should not merge until fixed.

## Key Principles

AI writes code in isolation. It does not know your system's architecture, your
traffic patterns, your failure modes, or your security requirements. Your job as
reviewer is to supply that context and reject code that ignores it.

AI-generated code passes tests because AI writes for the test, not for production.
The absence of test failures does not mean the code is correct.

Speed of generation is not a proxy for quality. A feature written in 10 minutes
that causes a 3-hour outage cost more than writing it properly in 60 minutes.

## Reference Files

- `references/checklist.md` - Full checklist with all 30+ review items
- `references/common-failures.md` - Patterns AI gets wrong most often, with examples
