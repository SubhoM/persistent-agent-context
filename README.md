# persistent-agent-context

Give any AI coding agent a persistent, self-updating memory of your
codebase — so it stops re-investigating the same repo from scratch every
session, and stops repeating mistakes that already happened.

Works with **Claude Code, GitHub Copilot, Codex CLI, Cursor**, and any
other agent that reads `AGENTS.md` or `CLAUDE.md`.

## The problem

Every fresh AI coding session re-derives repo structure, conventions, and
history from exploring files. That's expensive — commonly 45,000–200,000
tokens per issue — and it means the agent has no memory of a bug it
already diagnosed last week, or an approach the team already tried and
rejected.

## The approach

A single, portable set of instructions maintains a living knowledge base
in `/docs`, committed as part of the repo itself:

- **`AGENTS.md`** (repo root) — the cross-tool entry point, read natively
  by Codex, Copilot, Cursor, and most other agents.
- **`CLAUDE.md`** — a one-line import so Claude Code (which doesn't read
  `AGENTS.md` natively as of mid-2026) follows the same instructions.
- **`skills/repo-memory/SKILL.md`** — the richer, triggerable version of the same workflow
  for tools that support the [Agent Skills](https://github.com/agentskills/agentskills)
  format.
- **`/docs/*`** — the actual knowledge base the agent builds and
  maintains: architecture, conventions, past findings, decisions, and
  open questions.

The key discipline this enforces: the agent **edits existing sections
in place** rather than appending forever, so the knowledge base stays a
concise, current snapshot instead of an ever-growing changelog nobody
reads.

## Quick start

Copy `AGENTS.md`, `CLAUDE.md`, and (optionally) the `skills/repo-memory/` skill
folder (see "Use as a Skill") into your repo root:

```bash
curl -sL https://raw.githubusercontent.com/SubhoM/persistent-agent-context/main/AGENTS.md -o AGENTS.md
curl -sL https://raw.githubusercontent.com/SubhoM/persistent-agent-context/main/CLAUDE.md -o CLAUDE.md
```

Then start a session with any supported agent and ask it to document the
repo, or just start working — the instructions tell it to check for
`/docs` and generate it if missing.

## Use as a Skill

`SKILL.md` is a standard [Agent Skill](https://github.com/agentskills/agentskills)
(`name: repo-memory`). The `description` in its frontmatter tells the agent
when to trigger it: at session start, after significant findings, and before
finishing a task.

**Claude Code, one project** (shared with the team via git):

```bash
mkdir -p .claude/skills/repo-memory/references
curl -sL https://raw.githubusercontent.com/SubhoM/persistent-agent-context/main/skills/repo-memory/SKILL.md -o .claude/skills/repo-memory/SKILL.md
curl -sL https://raw.githubusercontent.com/SubhoM/persistent-agent-context/main/skills/repo-memory/references/token-efficiency-rationale.md -o .claude/skills/repo-memory/references/token-efficiency-rationale.md
```

**Claude Code, all your projects:** same commands with `~/.claude/skills/repo-memory/`
instead of `.claude/skills/repo-memory/` (on Windows PowerShell use
`$HOME\.claude\skills\repo-memory`).

Then start a session. The skill triggers on its own, or invoke it by name
(`/repo-memory`) or say "document this repo" / "check the knowledge base".

**Other tools:** Codex, Copilot and Cursor don't discover skills. Use
`AGENTS.md` (Quick start above); it carries the same workflow in condensed
form. Cursor and others that support skills can use the same `SKILL.md`
folder layout in their own skills directory.

**Best setup:** install the skill for the full workflow and also commit
`AGENTS.md` + `CLAUDE.md`, so tools without skill support behave the same.
Keep the two in sync if you edit either.

Check it works: ask the agent "what do you know about this repo?" in a fresh
session. It should read `docs/agent-context.md` before exploring code.

## What's in `/docs`

| File | Contents |
|---|---|
| `agent-context.md` | Read first, always. Project summary, stack, architecture snapshot, constraints, coding standards, recurring pitfalls. |
| `architecture.md` | System architecture, app flow, module relationships, integrations, deployment info. |
| `code-patterns.md` | Coding conventions, reusable patterns, testing patterns, examples. |
| `findings.md` | Repo discoveries, undocumented behavior, file locations, troubleshooting notes. |
| `issues-and-decisions.md` | Bugs and root causes, fixes, design decisions, tradeoffs. |
| `future-work.md` | Technical debt, open questions, known limitations. |

This repo's own [`docs/`](docs/agent-context.md) is a small worked example of
the output, generated with the pattern it ships.

## Lessons from long-running use

Tested on a multi-month app repo, the pattern's weak point was growth, not
discovery. Without limits, `agent-context.md` reached ~57 KB of dated status
entries and `issues-and-decisions.md` passed 1 MB. The instructions now add:

- `agent-context.md` stays a stable snapshot (short `CURRENT STATE` block,
  under ~15 KB); dated history lives in `issues-and-decisions.md`.
- Archive entries older than ~90 days once a file passes ~100 KB.
- A fixed entry shape (request, root cause, fix, verification).
- Non-memory docs (roadmaps, store listings, promo) are linked, not merged.
- Verification tags (`owner-confirmed`, `Needs Verification`) and recorded
  test results.
- Test rules: document how to run tests, add unit and integration tests for
  each feature, a regression test for each bug fix, and run the suite before
  finishing.
- A longer redaction checklist: emails, UUIDs, project refs, ticket numbers,
  user-revealing paths.

## Guardrails this pattern enforces

- **Edit, don't append** — every update revises existing sections instead
  of stacking a dated log entry, keeping token cost bounded over time.
- **Redaction pass** — before any write to `/docs`, the instructions
  require checking for secrets, credentials, and PII before they can be
  committed to a file agents (and anyone else) will read.
- **Freshness discipline** — the instructions require a documentation
  review before any task is considered complete, not just at some
  arbitrary interval.

These are process guardrails enforced by the instructions themselves, not
tooling — pairing this with a CI check that flags a stale or missing
`/docs` folder is a natural next step if you want harder enforcement.

## Why this helps

See [`skills/repo-memory/references/token-efficiency-rationale.md`](skills/repo-memory/references/token-efficiency-rationale.md)
for a worked example — a single documented issue that cost ~200,000
tokens to investigate the first time cost roughly 5,000 tokens to resolve
once the finding was captured. Your mileage will vary with how often
similar issues recur and how disciplined the update step stays.

## Prior art

This composes ideas that are common across the AI-agent-instruction-file
ecosystem — the [`agents.md`](https://agents.md) convention, Anthropic's
[Agent Skills](https://github.com/agentskills/agentskills) format, and
general docs-as-code practice. It isn't the first "living docs for
agents" pattern; if your team wants a more elaborate governance model
(ADRs, BDRs, architecture diagrams), it's worth also looking at projects
like [`living-docs-skill`](https://github.com/ejklock/living-docs-skill).
This one is intentionally minimal: one context file, five supporting
files, an edit-in-place rule, and a redaction step.

## Contributing

Issues and PRs welcome — especially real-world reports of what token or
time savings this produced (or didn't) on your repo, and edge cases the
redaction pass should catch but doesn't yet.

## License

MIT — see [LICENSE](LICENSE).
