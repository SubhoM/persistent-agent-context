# Findings

_Last Updated: 2026-10-18_

## Important file locations
- Instructions: `AGENTS.md`, `CLAUDE.md`, `skills/repo-memory/SKILL.md`.
- Token math: `skills/repo-memory/references/token-efficiency-rationale.md`.
- Skill install target in a host repo: `.claude/skills/repo-memory/`
  (project) or `~/.claude/skills/repo-memory/` (personal). See README.

## Field observations from a long-running app repo (2026-10-18)
- What: the pattern's weak point is growth, not discovery.
- Evidence: in that repo `agent-context.md` reached ~57 KB, `findings.md`
  ~83 KB, `future-work.md` ~340 KB, `issues-and-decisions.md` ~1.2 MB.
  `agent-context.md` itself noted that 100+ dated entries had to be moved out.
- Impact: reading "context first" stops being cheap; the 80% success bar is
  hard to meet when the file is a log.
- Action taken: size limits, archive rule, entry shape (`AGENTS.md`,
  `SKILL.md`).
- Also seen there: logs contained test-account emails, user UUIDs, cloud
  project refs and ticket numbers, so redaction guidance now lists them.
- Also seen there: the host repo had non-memory docs (roadmap, store listing,
  design history) and a promo folder; the pattern now says to link, not merge.

## Test setup in the same app repo (worked example)
- ~75 backend test files, one per feature, run with pytest; shared fixtures
  give an in-memory database and a test client; external auth and services
  are monkeypatched, so no network.
- Frontend: component tests (Vitest + Testing Library) and Playwright
  end-to-end tests that start the dev server and backend.
- Costly checks (paid model-call regression matrix) run by hand with a cost
  estimate and a confirmation flag; their helpers are unit-tested.
- Deliberately not automated: a flow needing a secret key; checked manually
  with a throwaway user that is deleted afterward.
- Fix entries named the regression test added and the suite result. Rules
  derived from this are in `AGENTS.md` "Tests".

## Skill vs AGENTS.md
- Codex/Copilot do not discover Agent Skills; they need `AGENTS.md`.
  Claude Code needs `CLAUDE.md` (import of `AGENTS.md`) or the skill.
