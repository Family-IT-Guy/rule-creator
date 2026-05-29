# Rule Creator — Reference

## The nine architecture-level principles

Summarized here so the skill is self-contained.

1. Trust the model with the shape, not the words — state the work and the judgment calls, not output phrases.
2. Brevity is a quality signal — over ~100 lines means you're controlling too much.
3. Philosophy first — one compass sentence for what the work *is*.
4. Anti-patterns with reasoning — name the failure *and why*, so the model generalizes.
5. Quality gates over compliance gates — name properties the output should have, not symptoms to avoid.
6. Persona at the global layer, not per-instruction.
7. Composition over inflation — reference companion files; keep each instruction focused.
8. The user is a competent agent — no performed warmth, no hand-holding, no theater.
9. The test changes shape — judge whether the output achieved the purpose, not whether it matched a structure.

## Harness → artifact map

Pick the artifact by what the harness exposes. Getting this wrong is the most common failure (anti-pattern 2 in SKILL.md).

### ChatGPT — browser or app

- **Config surface:** Custom Instructions (Settings → Personalization → Custom Instructions). Persistent across all chats.
- **Artifact:** a posture block (a few sentences). This is the universal lowest-common-denominator deliverable.
- **No** skills, **no** MCP, **no** autonomous file management. Anything requiring those is off the table for this harness — re-express it as posture text or a copy-paste path-guide.
- **Projects** (if the user has them): per-project instructions + pinned reference files. Use for task-scoped behavior that shouldn't apply globally.
- **Install:** the user pastes the block themselves (the agent can't write ChatGPT settings). Show the exact menu path and a screenshot of the post-state if available.

### Claude Code

- **Config surfaces, by behavior type:**
  - Always-on behavior across all projects → `~/.claude/CLAUDE.md` or a file in `~/.claude/rules/`.
  - Project-specific behavior → that project's `CLAUDE.md` or `<project>/.claude/rules/`.
  - A reusable capability invoked on demand → a skill (`~/.claude/skills/<name>/SKILL.md` for cross-project; `<project>/.claude/skills/` for project-local).
  - An *automated* behavior the harness must enforce ("whenever X, do Y") → a hook in `settings.json` (the harness runs hooks, not the model — memory/rules can't fulfill an automation).
  - External data/tools → an MCP server.
- **Install:** the agent can write most of these directly. Confirm before editing global config.

### codex

- **Config surface:** `AGENTS.md` (project or `~/.codex/AGENTS.md`), `~/.codex/config.toml`. codex reads `CLAUDE.md` as a fallback when `AGENTS.md` is absent (project_doc_fallback_filenames).
- **Cross-CLI pointer pattern:** for a user on multiple agentic harnesses, keep ONE rule set (the `.claude/` rules) and point `AGENTS.md` (and `GEMINI.md`) at it, rather than maintaining divergent copies. One source of truth, harness-specific pointers.

### Gemini CLI

- **Config surface:** `GEMINI.md`. Same cross-CLI pointer pattern applies.

### Other / unknown harness

- Ask what config surfaces it exposes (persistent instructions? per-project? tool/extension system?). Map to the nearest case above. If it only has a chat box with no persistent settings, the artifact is a copy-paste preamble the user pastes at the start of each session — flag that it won't persist.
