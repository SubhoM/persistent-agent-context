# Future Work

_Last Updated: 2026-10-18_

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
