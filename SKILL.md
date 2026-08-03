---
name: repo-memory
description: Establishes and maintains a persistent project knowledge base in the repo's /docs folder so any coding assistant — Claude Code, Copilot, Cursor, or similar — can understand the codebase, avoid repeating past investigations, and minimize token usage across sessions. Use this skill at the start of every development session to check for and read existing /docs, whenever no /docs knowledge base exists yet and one should be created, immediately after a significant discovery, bug root-cause, or architectural decision, and before completing any task to keep the documentation current. Also trigger when explicitly asked to "document this repo," "update the project docs," or "check the knowledge base."
---

# Repo Memory

Maintains a persistent source of truth for a project in its `/docs` folder,
so future sessions — by any coding assistant, not just one tool — can
understand the codebase, skip re-investigating what's already known, and
avoid repeating past mistakes. This is project memory that lives in the
repo itself, not in any single assistant's session.

## Startup process (run this first, every session)

1. Check whether `/docs` exists.
2. If it does not exist: create it and generate the full documentation
   structure below.
3. If it exists: read **`docs/agent-context.md` first**, then the other
   files as relevant to the current task. Do this before analyzing or
   modifying any code.
4. Use the documentation as project memory for the rest of the session —
   don't re-derive what's already documented.

## Documentation structure

### `docs/agent-context.md` — primary context file, read first
- Project summary and business purpose
- Technology stack
- Architecture snapshot
- Important constraints
- Coding standards
- Recurring pitfalls
- Active workstreams
- Onboarding notes for future sessions

### `docs/architecture.md`
- System architecture and application flow
- Major modules and component relationships
- Integrations
- Deployment / build information
- Infrastructure observations

### `docs/code-patterns.md`
- Coding conventions
- Reusable patterns and common implementation approaches
- State management patterns
- Data access patterns
- Testing patterns
- Examples and references

### `docs/findings.md`
- Repository discoveries
- Architectural findings
- Undocumented behavior
- Important file locations
- Framework-specific observations
- Troubleshooting notes

### `docs/issues-and-decisions.md`
- Bugs identified, with root causes and fixes implemented
- Design decisions and tradeoffs
- Lessons learned

### `docs/future-work.md`
- Technical debt
- Open questions
- Known limitations
- Future improvements
- Pending investigations

## Maintenance rules (apply throughout the session)

- Update documentation continuously as new information is discovered —
  immediately after a major finding, not batched at the end of session.
- **Prefer updating existing sections over creating duplicates.** This is
  what keeps the knowledge base from becoming a stale, ever-growing log —
  edit in place.
- Remove information that has since been proven incorrect; don't leave it
  contradicted by a newer note.
- Mark uncertain findings explicitly as `Needs Verification` rather than
  stating them as fact.
- For every significant discovery, record: what was found, the evidence,
  relevant file paths, the impact, and recommended actions if applicable.
- Add a `Last Updated` timestamp to every file you touch.

Before completing any task:
- Review the documentation for accuracy against what you now know.
- Update affected sections.
- Confirm the knowledge base reflects the current understanding of the
  repository — not a stale snapshot from earlier in the session.

## Redaction pass (required before every write)

Before writing or updating any file under `/docs`, scan the draft for
anything that shouldn't be committed to version control: API keys, tokens,
credentials, connection strings, customer or employee PII, or internal-only
business logic that shouldn't be broadly readable. Strip or generalize
matches — describe the shape of the issue rather than including literal
secrets.

## Documentation guidelines

- Record facts, not assumptions.
- Use short, searchable sections and bullet points wherever possible.
- Include file paths and code references.
- Avoid duplicating information across files — link between them instead.
- Preserve important historical decisions rather than deleting them when
  superseded; note that they were superseded and why.
- Keep documentation concise and optimized for AI consumption, not prose
  written for a human skim-reader.

## Success criteria

A brand-new session should be able to read `docs/agent-context.md` alone
and understand at least 80% of the project's architecture, conventions,
constraints, known issues, and current development direction — without
re-investigating the codebase.

## Why this matters (token economics)

Re-discovering context from scratch each session — semantic search, reading
components, trial-and-error discussion — commonly runs 45,000–200,000
tokens per issue. Reading a maintained knowledge base first typically drops
that to 2,000–15,000 tokens for the same issue: roughly a 70–90% reduction,
compounding across every developer and every session. See
`references/token-efficiency-rationale.md` for the full breakdown and
worked example this figure is based on.

## Notes

- Write documentation as plain, tool-agnostic markdown — this file is
  read by whatever assistant opens the repo next, not one specific tool.
- If the repo already has a `CONTRIBUTING.md` or architecture doc, don't
  duplicate it — link to it from the relevant `/docs` file instead.

## Cross-tool setup (Claude Code, Copilot, Codex, etc.)

This `SKILL.md` packages the *workflow* — when to run it and how to
maintain `/docs`. It doesn't by itself make Codex or Copilot pick it up
automatically, since those tools don't discover Claude Skills.

For actual cross-tool auto-loading at session start, pair this skill with:

- **`AGENTS.md`** at the repo root — read natively by Codex CLI, GitHub
  Copilot, Cursor, Windsurf, and most other agents. This should contain
  the condensed startup process and `/docs` pointer (see this skill's
  companion `AGENTS.md`).
- **`CLAUDE.md`** — Claude Code reads this, not `AGENTS.md`, natively.
  Keep it a one-line `@AGENTS.md` import (or a symlink) so Claude Code
  follows the same instructions without a second copy to maintain.

In short: `/docs/*` is the actual knowledge base content (fully
tool-agnostic); `AGENTS.md` + `CLAUDE.md` are the thin bootstrap files
that get each tool to read it; this `SKILL.md` is the richer, triggerable
version of the same workflow for tools that support Agent Skills.
