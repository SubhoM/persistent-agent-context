# Token efficiency rationale

Supporting detail for why `repo-memory` is worth the maintenance overhead.
Pulled from a real measured session; treat the multi-developer projections
as directional, not guaranteed — they scale with team size, session
frequency, and how disciplined the team is about keeping docs current.

## Per-session comparison

**Without documentation** — investigating an issue from scratch:
- Semantic search across the codebase: ~15,000 tokens
- Reading multiple component files: ~30,000 tokens
- Reading service files: ~10,000 tokens
- Trial-and-error discussion: ~20,000 tokens
- Re-explaining context: ~15,000 tokens
- **Total: ~90,000 tokens** for the first occurrence; ~45,000 for a repeat
  of a similar issue

**With documentation** — same issue, knowledge base already exists:
- Read the relevant doc file: ~8,000 tokens
- Reference a specific pattern/section: ~2,000 tokens
- Implementation help: ~5,000 tokens
- **Total: ~15,000 tokens** for the first occurrence; ~5,000 for a repeat

**Reduction: roughly 70–90% per issue**, with the largest gains on repeat
occurrences of a known pattern.

## Worked example (measured)

One real debugging session, start to resolution:
- Initial context + full conversation transcript: **~200,000 tokens**

The same issue, in a future session referencing the resulting doc:
- Doc lookup + solution: **~5,000 tokens**
- **Savings: ~195,000 tokens (98% reduction)** for that specific,
  now-documented issue

## Why the reduction happens

1. **Structured over scattered** — one organized file beats piecing
   together the same understanding from 30–50 source files.
2. **Direct answers over rediscovery** — "see Pattern 3" replaces reading
   ten files, inferring the pattern, and testing it again.
3. **No re-derivation** — root cause, solution, and examples are already
   written down; nothing has to be reasoned out twice.
4. **Fewer wrong turns** — precision from a direct reference cuts the
   trial-and-error tokens that dominate an undocumented investigation.

## Where the savings compound

- **Across developers**: every developer on a team hits the same
  undocumented issue at full cost without a shared doc; with one, every
  developer after the first pays only the lookup cost.
- **Across sessions**: an assistant's session context resets every
  conversation; a `/docs` file persists at a fraction of the token cost of
  reconstructing that context from a transcript.
- **Maintenance cost is small by comparison**: updating docs after a
  session typically costs a couple thousand tokens and a few minutes —
  far less than the cost of the next person rediscovering the same thing.

## Caveat

These numbers came from one team's Angular/Copilot session and should be
treated as an illustrative range, not a guaranteed multiplier — actual
savings depend on how often the same or similar issues recur, how
disciplined the team is about the maintenance step, and how well the docs
stay current (a stale doc that sends someone down the wrong path costs more
than no doc at all).
