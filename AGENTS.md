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

## Keeping the knowledge base small enough to read

Field experience (a long-running app repo): without these rules,
`agent-context.md` grew to ~57 KB of dated status entries and
`issues-and-decisions.md` to over 1 MB, defeating the point of reading
context first.

- **`agent-context.md` is a stable snapshot, not a changelog.** Keep a short
  `CURRENT STATE` block (a handful of bullets: what is live, what is in
  flight, what is blocked) and revise it in place. Target under ~15 KB.
- **Dated history belongs in `issues-and-decisions.md`**, newest entry first,
  one entry per event. When an entry in `agent-context.md` is shipped and
  stable, move its detail there and leave a one-line pointer.
- **Archive instead of growing forever.** When a file passes ~100 KB, move
  entries older than ~90 days to `docs/archive/<file>-<year>.md` and keep a
  one-line index of what was archived. Agents read the live file, not the
  archive, unless searching.
- **Entry shape** for bugs and decisions: request or symptom, root cause,
  fix with file paths, verification (tests run, result). Skip play-by-play.
- **Search before reading.** For large files, grep headings or keywords
  rather than loading the whole file.

## Other docs in `/docs`

Repos often also hold product, roadmap, store-listing, or design documents.
Those are not agent memory. Don't merge them into the six files above; list
them under a "Related docs" heading in `agent-context.md` and link to them.
One doc per purpose: add to the existing file (newest entry first) rather than
creating a new dated file per event. Keep non-code material such as marketing
or promo assets outside `/docs` (for example `/promo`) with its own README.

Large binary files the app doesn't use (videos, raw recordings, test output)
don't belong in git; store them elsewhere and record only the location
pattern, never a folder that also holds credentials.

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

Easy-to-miss items that real logs contained and should be generalized:
user email addresses and test-account names, user/session/row UUIDs, cloud
project references and storage hostnames, support ticket numbers, internal
absolute paths that reveal usernames, and balances or prices that are
internal business logic. Write "a test account" or "a session id", not the
literal value.

## Tests

- **Document how to test.** Record test commands, layout, and prerequisites
  (unit, integration, end-to-end; services or credentials needed, described
  by shape only) in `code-patterns.md`, with a one-line "how to run tests"
  bullet in `agent-context.md`.
- **Every new feature gets tests.** Unit tests for each function or
  behavior; add an integration test when it crosses modules, the database,
  or an external service.
- **Every bug fix gets a regression test** that fails without the fix. Name
  it in the `issues-and-decisions.md` entry.
- **Run the relevant suite before finishing a task** and record suite,
  count, and result in the entry. Never claim tests pass without running them.
- **Isolate tests.** Use shared fixtures (for example an in-memory database)
  and mock external services and auth so tests never hit the network.
  Keep test-only dependencies separate from production ones.
- **Separate cheap from costly.** Automated tests must be free and fast.
  Anything that spends money or calls live services (regression matrices,
  stress scripts) runs by hand, prints a cost estimate first, and needs an
  explicit confirmation flag above a set limit. Document when to run it.
- **Record what is not automated, and why**, with the manual check to use
  instead (for example flows needing a secret key that must not be committed).
  Never commit secrets in tests; delete any test users or data you create.
- Record test gotchas (config flags, fixture quirks) in `code-patterns.md`.
- If the repo has no tests, say so under known limitations in
  `future-work.md` and propose a minimal starting set rather than skipping.

## Verification claims

- Tag facts you did not observe yourself: "owner-confirmed <date>" for
  things the user told you, `Needs Verification` for anything unchecked.
- Record what was tested (suite, count, result) next to each fix so a later
  session knows how far to trust it.
- Record the exact command forms that work for permissioned or automated
  operations (for example allow-listed commands that stop matching when
  prefixed with `cd` or `sleep`) under an "Ops notes" bullet.

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
