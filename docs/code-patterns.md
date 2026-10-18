# Code Patterns

_Last Updated: 2026-10-18_

No application code. These are the conventions for the instruction files.

## Instruction-file conventions
- Imperative, numbered steps for procedures; bullets for rules.
- Each rule appears in full in `SKILL.md` and condensed in `AGENTS.md`.
  When changing one, check the other (see `docs/agent-context.md` Constraints).
- Wrap lines near 75 columns, matching existing files.
- Frontmatter in `SKILL.md` only: `name`, `description`. The description is
  the trigger text, so list the situations that should invoke the skill.

## Doc-entry pattern (for host repos)
- Bug/decision entry: request or symptom, root cause, fix with file paths,
  verification (tests, result). Newest first in `issues-and-decisions.md`.
- Uncertain facts: `Needs Verification`. User-reported facts:
  `owner-confirmed <date>`.

## Testing rules (for host repos, defined in AGENTS.md "Tests")
- Document test commands in `code-patterns.md`; feature = unit (+ integration
  if cross-module/external); bug fix = regression test; run suite before done.

## Testing this repo
- None automated. Manual check: run an agent in a scratch repo with the files
  copied in and confirm it creates/reads `/docs` (Needs Verification: not
  yet recorded as done).
