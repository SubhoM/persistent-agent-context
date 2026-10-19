# Future Work

_Last Updated: 2026-10-18_

## Next session (planned)
- Decide whether to soften the "70-90% per issue" claim in `README.md` and
  `skills/repo-memory/SKILL.md`; the A/B run showed ~13% mean cost saving on
  day-one docs (`skills/repo-memory/references/token-efficiency-rationale.md`).
- Optional: multi-session test of remembered bugs/decisions (the untested
  main benefit), and re-run on an unfamiliar repo.
- Marketplace listing: not started. Open points are in the earlier checklist
  (verify the marketplace's terms; decide free vs. paid extras).

## Ideas
- CI check that flags a missing or stale `/docs` (README mentions it as a
  natural next step).
- Script to report `/docs` file sizes and warn past the ~15 KB / ~100 KB
  limits.
- Script to scan `/docs` for emails, UUIDs, tokens before commit (redaction
  enforcement is currently instructions only).
- Template `/docs` skeleton files users can copy instead of having the agent
  generate them.

## Open questions
- Are the 15 KB / 100 KB / 90-day thresholds right? Derived from one repo.
  Needs Verification on others.
- Does Claude Code still ignore `AGENTS.md` natively? Needs Verification.
- Real-world token savings beyond the single worked example are unmeasured.

## Known limitations
- All guardrails are instructions, not enforced tooling.
- `AGENTS.md` / `SKILL.md` overlap must be kept in sync by hand.
