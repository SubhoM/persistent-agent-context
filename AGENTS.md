# Agent Instructions

This repo maintains a persistent knowledge base in `/docs` so any coding
agent — Codex, GitHub Copilot, Claude Code, Cursor, or similar — starts
with full project context instead of re-investigating the codebase every
session.

## Before doing anything else

1. Check whether `/docs` exists.
   - If it does not: create it and generate the structure below.
   - If it does: read `docs/agent-context.md` first, then whichever
     other files are relevant to the current task.
2. Use that documentation as project memory for this session — don't
   re-derive what's already documented.

## `/docs` structure

- **`agent-context.md`** — read this first, always. Project summary,
  business purpose, tech stack, architecture snapshot, constraints, coding
  standards, recurring pitfalls, active workstreams.
- **`architecture.md`** — system architecture, app flow, major modules,
  component relationships, integrations, deployment/build info.
- **`code-patterns.md`** — coding conventions, reusable patterns, state
  management, data access, testing patterns, examples.
- **`findings.md`** — repo discoveries, undocumented behavior, important
  file locations, framework-specific notes, troubleshooting.
- **`issues-and-decisions.md`** — bugs and root causes, fixes implemented,
  design decisions, tradeoffs, lessons learned.
- **`future-work.md`** — technical debt, open questions, known
  limitations, pending investigations.

## Keeping it current

- Update docs continuously as you discover things — immediately after a
  major finding, not batched at session end.
- **Edit existing sections rather than appending new ones.** This is what
  keeps the knowledge base from turning into a stale, ever-growing log.
- Remove anything proven incorrect; mark uncertain findings
  `Needs Verification` instead of stating them as fact.
- For every significant discovery, record: what was found, the evidence,
  relevant file paths, the impact, and recommended action if any.
- Add a `Last Updated` timestamp to any file you touch.
- Before finishing a task, review and update affected `/docs` files so
  the knowledge base reflects current understanding, not a stale snapshot.

## Redaction — required before every write to `/docs`

Never commit API keys, tokens, credentials, connection strings, customer
or employee PII, or internal-only business logic to these files. Describe
the shape of an issue rather than including literal secrets.

## Style

Record facts, not assumptions. Short, searchable sections. Bullet points
over prose. Include file paths and code references. Don't duplicate
information across files — link between them instead. Optimize for AI
consumption, not a human skim-read.

## Success bar

A brand-new session should be able to read `docs/agent-context.md`
alone and understand at least 80% of the project's architecture,
conventions, constraints, known issues, and current direction — without
re-investigating the codebase.
