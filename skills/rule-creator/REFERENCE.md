# Rule Creator — Reference

How to design instructions that make an LLM behave competently without over-scripting it. The deliverable is text that acts *on* an LLM — a system prompt, CLAUDE.md/AGENTS.md, a skill, a posture block. The only reader is the model, so design for the model, not a human reader: anything that explains, justifies, or narrates to a reader is dead weight.

## The nine principles

1. **Trust the model with the shape, not the words.** State the kind of work and the judgment calls; don't script output phrases. A banned-phrase list suppresses one symptom and teaches nothing general — name the property that produces the bad phrases instead and let the model police itself against it.
2. **Brevity is a quality signal, not a tradeoff.** Over ~100 lines is evidence the instruction is controlling more than it should. Length usually means accumulated "Do NOT" patches that a clear *why* would have prevented.
3. **Philosophy first, procedure second.** One compass sentence for what the work *is*; the model derives a hundred small decisions from it. If you can't write that sentence (or it's as empty as "be helpful"), the instruction has no center.
4. **Anti-patterns with reasoning beat prohibitions without.** Name the failure *and why it fails* — the model generalizes from the why to variants you never enumerated. A reasonless "Do NOT" is pattern-matching against past failures, not design.
5. **Quality gates over compliance gates.** Checklists name properties the output should *have*, not symptoms to avoid. Properties are stable; symptom lists catch yesterday's failures and miss today's.
6. **Persona belongs at the global layer, not per-instruction.** Declare identity once (global setup); each skill inherits it and gets work-shaped instructions only. Per-skill persona restatement drifts and conflicts.
7. **Composition over inflation.** Cross-reference companion files for depth; keep each instruction focused on one concern. Inlining every related topic forces the model to load all of it every invocation and makes editing one concern a whole-document rewrite. And when you cross-reference, the pointer names only the condition that should make the model load the target — it never restates the target's content, because restating both bloats the instruction and defeats the lazy-loading the reference exists to provide.
8. **The user is also a competent agent.** Don't perform warmth, hand-hold obvious steps, or gate work behind unnecessary intake. Scripted welcomes and reassurances are competence theater — cut them.
9. **The test changes shape.** When instructions are principled, the test is "did the output achieve the purpose for this user?" (judgment per run), not "did the model comply with my structure?" (transcript shape). Optimizing for compliance makes instructions longer and more brittle.

## Building a new instruction

Work in this order; the result lands under 100 lines:

1. Description + triggers — one sentence: what it does, when to use it.
2. Philosophy sentence — what the work *is*.
3. Anti-patterns with reasoning — 2–4 named failure modes, each with the property it violates.
4. Workflow as shaped phases — 2–5 phases naming *what* happens and *what judgment* the model brings, not phrase scripts.
5. Quality gates as properties — 3–6 items naming what the output should be.
6. Companion references — orthogonal concerns live in their own files.
7. Final cut — delete every word that explains, justifies, or narrates rather than instructs.

Over 100 lines → revisit principles 1, 2, 7.

## Harness → artifact map

Pick the artifact by what the harness exposes. Getting this wrong is the most common failure (the "Wrong artifact for the harness" anti-pattern in SKILL.md).

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

- **Config surface:** `AGENTS.md` (project or `~/.codex/AGENTS.md`), `~/.codex/config.toml`. codex reads only `AGENTS.md` by default; it reads `CLAUDE.md` when `AGENTS.md` is absent only if `project_doc_fallback_filenames = ["CLAUDE.md"]` is set in `~/.codex/config.toml` (the default is an empty list).
- **Cross-CLI pointer pattern:** for a user on multiple agentic harnesses, keep ONE rule set (the `.claude/` rules) and point `AGENTS.md` (and `GEMINI.md`) at it, rather than maintaining divergent copies. One source of truth, harness-specific pointers.

### Gemini CLI

- **Config surface:** `GEMINI.md`. Same cross-CLI pointer pattern applies.

### Other / unknown harness

- Ask what config surfaces it exposes (persistent instructions? per-project? tool/extension system?). Map to the nearest case above. If it only has a chat box with no persistent settings, the artifact is a copy-paste preamble the user pastes at the start of each session — flag that it won't persist.
