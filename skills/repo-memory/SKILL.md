---
name: repo-memory
license: MIT
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

## Keeping the knowledge base small enough to read

Lesson from a long-running app repo: left alone, `agent-context.md` grew to
~57 KB of dated status entries and `issues-and-decisions.md` to over 1 MB.
At that size the "read context first" step costs more than it saves.

- `agent-context.md` is a stable snapshot, not a changelog. Keep a short
  `CURRENT STATE` block (what is live, in flight, blocked) and revise it in
  place. Target under ~15 KB.
- Dated history goes in `issues-and-decisions.md`, newest first, one entry
  per event. Once something shipped is stable, move its detail there and
  leave a one-line pointer in `agent-context.md`.
- Past ~100 KB, move entries older than ~90 days to
  `docs/archive/<file>-<year>.md` and keep a one-line index in the live file.
- Entry shape: request or symptom, root cause, fix with file paths,
  verification (tests run and result). No play-by-play.
- For large files, grep headings or keywords instead of loading everything.
- Product, roadmap, store or design documents are not agent memory. Don't
  merge them into the six files; list them under "Related docs" in
  `agent-context.md`. One doc per purpose, add to it rather than creating a
  dated file per event. Keep marketing/promo material outside `/docs`.
- Keep large unused binaries (videos, raw recordings) out of git, and never
  record a storage location that also holds credentials.

## Tests

- Document how to test: commands, layout and prerequisites (unit,
  integration, end-to-end; needed services or credentials, described by
  shape only) in `code-patterns.md`, plus a one-line "how to run tests"
  bullet in `agent-context.md`.
- Every new feature gets tests: unit tests for each function or behavior,
  and an integration test when it crosses modules, the database, or an
  external service.
- Every bug fix gets a regression test that fails without the fix; name it
  in the `issues-and-decisions.md` entry.
- Run the relevant suite before completing a task and record suite, count
  and result. Never claim tests pass without running them.
- Isolate tests: shared fixtures (e.g. an in-memory database), mocked
  external services and auth, no network. Keep test-only dependencies apart
  from production ones.
- Separate cheap from costly: automated tests are free and fast. Anything
  that spends money or calls live services (regression matrices, stress
  scripts) runs by hand, prints a cost estimate first, and needs an explicit
  confirmation flag above a set limit. Document when to run it.
- Record what is not automated and why, with the manual check to use
  instead (e.g. flows needing a secret key that must not be committed). Never
  commit secrets in tests; delete test users or data you create.
- Record test gotchas (config flags, fixture quirks) in `code-patterns.md`.
- If the repo has no tests, record that under known limitations in
  `future-work.md` and propose a minimal starting set instead of skipping.

## Verification claims

- Tag facts you did not observe: "owner-confirmed <date>" for what the user
  told you, `Needs Verification` for anything unchecked.
- Record what was tested (suite, count, result) beside each fix.
- Record exact command forms that work for allow-listed or automated
  operations (e.g. a rule stops matching if the command is prefixed with
  `cd` or `sleep`) in an "Ops notes" bullet.

## Redaction pass (required before every write)

Before writing or updating any file under `/docs`, scan the draft for
anything that shouldn't be committed to version control: API keys, tokens,
credentials, connection strings, customer or employee PII, or internal-only
business logic that shouldn't be broadly readable. Strip or generalize
matches — describe the shape of the issue rather than including literal
secrets.

Easy to miss, and present in real logs: user emails and test-account names,
user/session/row UUIDs, cloud project refs and storage hostnames, support
ticket numbers, absolute paths that reveal usernames, and internal balances
or pricing. Write "a test account" or "a session id" instead of the value.

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
