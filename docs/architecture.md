# Architecture

_Last Updated: 2026-10-18_

## Components
| File | Role | Read by |
|---|---|---|
| `AGENTS.md` | Condensed cross-tool instructions | Codex, Copilot, Cursor, Windsurf, most agents |
| `CLAUDE.md` | One-line `@AGENTS.md` import | Claude Code |
| `skills/repo-memory/SKILL.md` | Full workflow, triggerable (frontmatter `name: repo-memory`) | Tools supporting Agent Skills |
| `skills/repo-memory/references/token-efficiency-rationale.md` | Token-savings worked example, linked from `SKILL.md` and README | Humans, on demand |
| `README.md` | Overview, install, skill usage | Humans |
| `docs/*` | This repo's own knowledge base (example of the output) | Agents working on this repo |

## Flow in a host repo
1. Agent session starts; tool loads `AGENTS.md` (or `CLAUDE.md` -> `AGENTS.md`),
   or triggers `SKILL.md` by its description.
2. Agent checks for `/docs`. Missing: generate the six files. Present: read
   `docs/agent-context.md` first, then relevant files.
3. During work: edit sections in place, redact, add `Last Updated`.
4. Before finishing: review affected docs.

## Knowledge base layout (created in host repo)
- `agent-context.md`, `architecture.md`, `code-patterns.md`, `findings.md`,
  `issues-and-decisions.md`, `future-work.md`.
- Optional: `docs/archive/` for old entries; separate product/store/promo docs
  linked from `agent-context.md`.

## Deployment
- None. Consumers copy files or install the skill (README).
