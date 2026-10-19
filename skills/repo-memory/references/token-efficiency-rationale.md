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

## Controlled A/B measurement (2026-10-18)

Setup: a shallow clone of the public `psf/requests` repo (130 files, ~427 KB
under `src/` and `tests/`), run through the Claude Code CLI with exact usage
reported per run. Arm A had no docs. Arm B had `AGENTS.md`/`CLAUDE.md` plus
`/docs` generated once by the agent. Each run was a fresh session with
read-only tools and the same prompt, 3 questions x 2 repetitions per arm.

One-time cost to build the docs: **$0.68, 23 turns, ~3.7 minutes**, producing
~30 KB across six files (`agent-context.md` ~7.5 KB).

| Question type | Cost A -> B | Turns A -> B | Time A -> B |
|---|---|---|---|
| Architecture overview + call flow | $0.34 -> $0.23 (-32%) | 10.5 -> 6.5 (-38%) | 55s -> 37s (-33%) |
| Conventions (tests, Python versions) | $0.24 -> $0.23 (-6%) | 13 -> 10 (-23%) | 49s -> 39s (-20%) |
| Change impact (where to add a default timeout) | $0.51 -> $0.50 (-3%) | 20 -> 20 (0%) | 114s -> 115s (0%) |
| **All questions, mean** | **$0.37 -> $0.32 (-13%)** | **14.5 -> 12.2 (-16%)** | **73s -> 64s (-12%)** |

- Raw token totals (input + cache + output, summed over turns) fell ~19% on
  average (763K -> 615K), and 55% on the architecture question. Cost is the
  fairer figure: most of the total is cached context re-read on every turn.
- Answer quality: spot-checked the change-impact answers; both arms named the
  same files, functions and line numbers. Not all 12 answers were graded.
- Break-even: saving ~$0.05 per question on average means the $0.68 build
  pays back after roughly 14 questions, or ~6 for architecture-style ones.
  It is a one-time cost shared by every later session and developer.
- Takeaway: docs help most on orientation questions (what exists, how it
  fits together) and barely at all when the task needs reading the exact code
  to change (arm B still read the same source files).

Limits, stated plainly:
- One repo, two repetitions per cell. Differences under ~10% are within noise.
- `requests` is famous and likely in the model's training data, which makes
  arm A look better than an unfamiliar private codebase would.
- Tested only what the docs capture on day one. The claimed biggest benefit,
  remembering past bugs and decisions across sessions, was not tested.
  Needs Verification.
- Docs came from one generation run. The repo's own Sphinx `docs/` folder
  shares the `/docs` path with the knowledge base; the agent coexisted with it,
  but repos like this may want a different path.
- This does not reproduce the 70-90% and 98% figures above; those come from
  a different, single-session measurement.

## The reading cost: when the docs themselves get expensive

The savings above assume the docs stay small. A second data point, a
multi-month app repo (October 2026), shows what happens when they don't.
File sizes are measured; token counts are rough estimates at ~4 bytes per
token, not measured usage.

| File | Size | Approx. tokens to read in full |
|---|---|---|
| `agent-context.md` (the "read first" file) | ~57 KB | ~14,000 |
| `findings.md` | ~83 KB | ~21,000 |
| `future-work.md` | ~343 KB | ~85,000 |
| `issues-and-decisions.md` | ~1.25 MB | ~310,000 |

- The "read first" file alone cost about as much as the ~15,000-token
  documented-session figure above, before any task work. Its opening block
  was a long list of dated status entries rather than a snapshot.
- Reading `issues-and-decisions.md` in full would exceed a typical context
  window, so it can only be searched, never read whole.
- Those sizes came from logging every change with its full diagnosis and no
  archive step, which is why the size limits (about 15 KB for the context
  file, archive past about 100 KB) and the search-before-reading rule exist.
- Targeting ~15 KB for `agent-context.md` puts the unavoidable startup read
  near ~4,000 tokens, and searching headings or keywords keeps lookups in
  the low thousands even when the history file is large.
- Per-issue lookups stayed cheap in practice because entries share one shape
  (request, root cause, fix with file paths, verification), so a keyword
  search lands on the answer. Whether that matched the 70-90% reduction above
  was not measured. Needs Verification.

Net: the reduction depends on the read cost staying bounded. Maintenance
discipline is part of the savings, not an extra.

## Caveat

The first set of numbers came from one team's Angular/Copilot session, and
the second from one app repo's file sizes. Both should be treated as
illustrative ranges, not a guaranteed multiplier — actual
savings depend on how often the same or similar issues recur, how
disciplined the team is about the maintenance step, and how well the docs
stay current (a stale doc that sends someone down the wrong path costs more
than no doc at all).
