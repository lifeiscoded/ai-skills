---
name: spec-document-reviewer
description: Review software specification documents for completeness, consistency, clarity, scope fit, testability, and implementation readiness. Use when a spec is drafted or revised and must be validated before writing an implementation plan.
---

# Spec Document Reviewer

## Overview

Review one spec file and return a strict go/no-go assessment for planning readiness.

## Review Workflow

1. Read the full target spec file.
2. Check for blocking issues in this order:
- Completeness: TODOs, TBD, placeholders, missing required sections.
- Consistency: conflicting statements, incompatible constraints.
- Scope fit: too broad for one implementation plan, or mixed independent subsystems.
- Clarity: ambiguous requirements, undefined terms, missing acceptance criteria.
- Architecture quality: unclear boundaries, weak interfaces, coupled units that are hard to test independently.
- Error handling and edge cases: failure modes, validation behavior, fallback behavior.
- YAGNI: unrequested features or over-engineering.
3. Decide status:
- `Approved` only if no blocking issues remain.
- `Issues Found` if any blocking issue exists.
4. Return output using the exact format below.

## Output Format

```md
## Spec Review

**Status:** Approved | Issues Found

**Issues (if any):**
- [Section or heading]: [specific issue] - [why it blocks planning]

**Recommendations (advisory):**
- [non-blocking improvement]
```

## Quality Bar

- Be concrete; cite exact section names.
- Do not rewrite the spec unless asked.
- Keep recommendations separate from blockers.
- Prefer minimal, actionable feedback over broad commentary.
