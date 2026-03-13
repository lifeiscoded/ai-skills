---
name: token-balanced-docs
description: Use this skill for ANY documentation task — writing code comments, JSDoc/docstrings, API specs, READMEs, design specs, feature specs, or end-user docs. Trigger whenever the user says "document this", "write a README", "add comments", "update the spec", "generate docs", "document the API", or any request to produce written technical documentation. Also use proactively when documentation tasks are large enough to risk hitting context limits — scan structure before reading full files, chunk work by module, and reuse established patterns instead of re-analyzing.
---

# Token-Balanced Documentation

This skill produces high-quality documentation efficiently by sizing the approach to the task. Small tasks get direct treatment. Large tasks get chunked to avoid wasted context.

---

## Step 1: Assess Scope

Before writing anything, identify the documentation target and size:

| Scope | Signal | Approach |
|---|---|---|
| **Small** | Single function, class, or file <200 lines | Read fully, document in one pass |
| **Medium** | Module or 2–5 related files, 200–1000 lines | Scan exports/signatures first, read details selectively |
| **Large** | Multiple modules, entire codebase, or >1000 lines | Map structure with Glob/Grep, process one module at a time |

For **large tasks**, always start with structure mapping — use `Glob` to list files and `Grep` to find exports, route definitions, class names, and public APIs. This costs very few tokens and prevents you from reading unnecessary implementation details.

---

## Step 2: Choose the Right Documentation Type

Match the output format to what's being documented.

### Code Documentation (functions, classes, modules)
- **TypeScript/JavaScript**: JSDoc (`/** ... */`)
- **Python**: docstrings (`"""..."""`)
- **Other**: follow the language convention

Every function/method doc should include:
1. **What it does** — one clear sentence
2. **Parameters** — name, type, description (skip `any` generics — be specific)
3. **Return value** — type + what it represents
4. **Throws** — any errors that can be raised
5. **Example** — at least one realistic call with expected output (skip only for trivial getters/setters)

Every class doc should include:
1. **Purpose** — what problem this class solves
2. **Constructor params** — same as function params above
3. **Key methods** — brief overview of the most important public methods

Every module/file doc should include:
1. **What this module exports and why it exists**
2. **Key dependencies** — what external systems or modules it integrates

---

### API Documentation (REST endpoints)

For each endpoint, always produce:

```
METHOD /path/to/endpoint

Description: What this endpoint does and when to use it.

Auth: Required? (Bearer JWT / API key / none)

Request:
  Headers: { "Content-Type": "application/json" }
  Body:
    field (type, required/optional): description

Response 200:
  Body:
    field (type): description

Errors:
  400 Bad Request — when...
  401 Unauthorized — when...
  404 Not Found — when...

Example:
  curl -X METHOD https://host/path \
    -H "Authorization: Bearer <token>" \
    -d '{"field": "value"}'
```

Never truncate error codes or example payloads. Real-world usage requires complete schemas.

---

### README

Structure every README in this order:

1. **Project name + one-sentence summary**
2. **Why** — what problem it solves (2–4 sentences, not marketing copy)
3. **Prerequisites** — what must already be installed/configured
4. **Quick start** — minimum steps to get something running. Write these as a **markdown numbered list** (`1.`, `2.`, `3.`) with each step on its own line *outside* any code block. Putting step labels as comments inside a bash code block (e.g. `# 1. Install`) doesn't count — the numbered list must be in prose markdown so it renders as visible steps.
5. **Configuration** — key environment variables or config files
6. **Usage examples** — at least one end-to-end example
7. **Project structure** — directory tree with one-line descriptions (for codebases)
8. **Contributing** (optional)
9. **License** (optional)

Keep it honest and precise. Developers trust READMEs that tell them what won't work as much as what will.

---

### Design / Feature Spec

Structure every feature spec as:

1. **Problem statement** — what user pain or gap exists
2. **Proposed solution** — high-level description of the approach
3. **Technical design** — how it will be implemented (data model changes, API changes, component structure)
4. **Key decisions** — design choices made and the trade-offs considered
5. **Out of scope** — what this spec explicitly does NOT cover (prevents scope creep)
6. **Open questions** — unresolved decisions (if any)

---

### End-User / Admin Documentation

Structure as task-oriented guides:

1. **What this page covers** — one sentence
2. **Prerequisites** — what the reader needs to know or have set up
3. **Steps** — numbered, imperative ("Click Save", not "You should click Save")
4. **Expected result** — what the user sees after each key step
5. **Troubleshooting** — common errors and how to resolve them

---

## Step 3: Token Efficiency Rules

These rules prevent wasteful context usage, especially on large tasks:

### Scan before reading
For any task involving 3+ files, use `Glob` and `Grep` to map structure before reading full file contents. Identify what actually needs documentation vs. what already has it.

### Never re-read what you've already processed
If you've read a file and extracted what you need, don't read it again. Take notes in your response as you go.

### Process in logical batches
For multi-module documentation, complete one module fully before moving to the next. Don't interleave reads across unrelated modules.

### Reuse established patterns
If the codebase already has a doc style (JSDoc, a specific README format, an existing spec template), match it. Don't re-analyze from scratch — grep for an existing example and follow it.

### Don't document the obvious
Skip boilerplate that adds noise:
- Getters/setters that do exactly what they say
- Constructor params that directly mirror field names with no transformation
- Re-exported types that are already documented upstream

---

## Step 4: Quality Checklist

Before finishing, verify:

- [ ] No "..." used as a content omission placeholder — every section must be complete. Exception: `...` is acceptable *inside* realistic example payloads to abbreviate long HTML strings (e.g. `"html": "<div>...</div>"`) — that's showing the shape of data, not skipping documentation.
- [ ] All examples are runnable/realistic (not `foo`, `bar`, `test123`)
- [ ] Types are specific — `string`, `UserId`, `SignatureHtml`, not `any`/`object`
- [ ] Error cases are documented, not just happy paths
- [ ] Docs match the current code (if updating existing docs, re-read the implementation first)
- [ ] Format matches the language/project convention

---

## Common Patterns by Project Type

### Node.js / TypeScript backend
- JSDoc on all exported functions and classes
- Inline comments only for non-obvious logic (not for `// increment counter`)
- `README.md` at project root + shorter `README.md` in major subdirectories if warranted

### React / Frontend
- JSDoc on custom hooks and utility functions
- Props interfaces should have inline TSDoc comments on each field
- Component files don't need JSDoc on the component itself if the file name is self-explanatory

### REST APIs
- Maintain a central `API_SPEC.md` or OpenAPI spec — don't scatter API docs across README files
- Each route file should have a brief module comment explaining which resource it manages

### Database / ORM
- Schema models should have field-level comments for anything non-obvious
- Migrations should have a comment block at the top: what changed and why
