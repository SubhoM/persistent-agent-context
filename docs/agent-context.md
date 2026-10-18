# Agent Context

_Last Updated: 2026-10-18_

Read this first. This repo documents itself with the pattern it ships
(dogfooding), so `/docs` here is also a worked example for adopters.

## CURRENT STATE
- Instruction set is complete and published: `AGENTS.md`, `CLAUDE.md`,
  `skills/repo-memory/SKILL.md`, `README.md`, `skills/repo-memory/references/token-efficiency-rationale.md`.
- 2026-10-18: added "keep it small" rules, verification tags and a longer
  redaction checklist, based on a long-running app repo (details:
  `docs/issues-and-decisions.md`).
- 2026-10-18: added this `/docs` folder and README instructions for using the
  repo as a Skill.
- No code, no build, no tests, no CI. Everything is markdown.

## Project summary
- Portable instructions that make any coding agent (Claude Code, Copilot,
  Codex, Cursor) maintain a persistent knowledge base in the host repo's
  `/docs`, so sessions stop re-investigating the codebase.
- Business purpose: cut tokens per session (see
  `skills/repo-memory/references/token-efficiency-rationale.md`) and stop repeated mistakes.
- Distribution: copy files into a target repo (README quick start) or install
  `SKILL.md` as an Agent Skill.

## Tech stack
- Markdown only. MIT licensed (`LICENSE`).

## Architecture snapshot
- See `docs/architecture.md`. Three layers: bootstrap files (`AGENTS.md`,
  `CLAUDE.md`), the triggerable skill (`SKILL.md`), the knowledge base
  (`/docs`, created in the host repo).

## Constraints
- `AGENTS.md` and `SKILL.md` carry overlapping rules and must stay in sync.
  `AGENTS.md` is the condensed version; `SKILL.md` is the fuller one.
- `CLAUDE.md` must remain a one-line `@AGENTS.md` import (no second copy).
- `AGENTS.md` is loaded every session in host repos, so keep it short.
- Never put secrets or PII in docs; see the redaction section in `AGENTS.md`.

## Coding standards (for editing these files)
- Bullets over prose, short sections, tool-agnostic wording.
- Edit existing sections in place; add a `Last Updated` date to touched files.
- Link between docs instead of duplicating.

## Recurring pitfalls
- Knowledge bases grow unbounded unless size rules are followed (observed in
  a real repo: 57 KB context file, 1 MB decisions log).
- Rule changes made in `AGENTS.md` but not `SKILL.md` (or the reverse) drift.
- Claude Code reads `CLAUDE.md`, not `AGENTS.md`, natively (README claim,
  mid-2026; Needs Verification against current Claude Code docs).

## Related docs
- `README.md` (user-facing overview and install steps)
- `skills/repo-memory/references/token-efficiency-rationale.md` (token math)

## Active workstreams
- See `docs/future-work.md`.
