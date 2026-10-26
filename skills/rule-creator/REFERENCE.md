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

## Where an instruction goes

Pick the artifact by what the harness exposes. Getting this wrong is the most common failure (the "Wrong artifact for the harness" anti-pattern in SKILL.md).

Each kind of behavior needs a particular kind of surface:

- **Always on, across all work** → the instructions the harness loads in every session (a global instruction file, a rules folder, or a custom-instructions box).
- **One project only** → that project's instruction file or per-project instructions.
- **A capability used on demand** → a skill, or the harness's nearest equivalent (a reference file the model consults, a saved prompt).
- **An automated behavior the harness must enforce** ("whenever X, do Y") → a hook or other harness-run automation. A rule the model reads cannot guarantee it, because the harness runs automations and the model does not.
- **External data or tools** → the harness's tool or connector system.

Find out which of these surfaces the user's harness has before choosing: read its current documentation, read its config files where the agent can reach them, or ask the user. Paths, menus and size limits change between releases, so establish them in the session rather than from memory. A harness with only a chat box and no persistent settings gets a preamble the user pastes at the start of each session; say that it won't persist.

- **Install:** the agent writes what it can reach after confirming, and gives the exact text and its destination for anything it can't, such as a settings screen.
- **Several harnesses:** keep one rule set in one place and point each harness's instruction file at it, rather than maintaining copies that drift apart.
