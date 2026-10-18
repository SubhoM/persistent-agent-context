# Issues and Decisions

_Last Updated: 2026-10-18_

Newest first.

## 2026-10-18: Move skill into skills/repo-memory/
- Why: marketplaces and skill installers expect a folder named after the
  skill (`repo-memory/SKILL.md`), not a root-level `SKILL.md`.
- Change: `SKILL.md` and `references/` moved to `skills/repo-memory/`;
  README install URLs and docs paths updated. `AGENTS.md`/`CLAUDE.md` stay
  at the root. Frontmatter gained `license: MIT`.

## 2026-10-18: Add test instructions
- Gap: docs only recorded tests, never told agents to write or run them.
- Decision: added a "Tests" section to `AGENTS.md` and `SKILL.md` (document
  commands, tests per feature, regression test per fix, run before finishing).
- Verification: manual read-through; the earlier scratch-repo run predates
  this change.

## 2026-10-18: Add growth controls from field experience
- Symptom: in a real app repo the docs became an append-only log (see
  `docs/findings.md`).
- Decision: keep `agent-context.md` a snapshot with a short `CURRENT STATE`;
  put dated history in `issues-and-decisions.md`; archive past ~100 KB; fixed
  entry shape; link non-memory docs instead of merging.
- Also added: verification tags, ops-notes bullet, expanded redaction list.
- Files: `AGENTS.md`, `SKILL.md`, `README.md`.
- Tradeoff: `AGENTS.md` is longer (loaded every session) but the added text
  prevents far larger files later.
- Verification: manual read-through only.

## 2026-10 (initial): CLAUDE.md as one-line import
- Decision: `CLAUDE.md` contains `@AGENTS.md` so there is one copy to maintain.
- Why: Claude Code reads `CLAUDE.md`, other agents read `AGENTS.md`.

## 2026-10 (initial): Edit in place, minimal file set
- Decision: six docs files plus edit-in-place rule, rather than a heavier
  ADR/BDR governance model (README "Prior art").
