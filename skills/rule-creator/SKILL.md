---
name: rule-creator
description: Write or rewrite an instruction that changes how an LLM behaves — a custom-instructions block, a CLAUDE.md/AGENTS.md/GEMINI.md rule, or a skill — fitted to the user's actual environment and proven with a before/after demo. Use when the user wants their AI to start or stop doing something, asks to write/fix a rule or system prompt, or wants to make a model behave more reliably.
---

# Rule Creator

Produce the smallest instruction that reliably changes the model's behavior as the user intends, in the form their harness can actually use — and prove it changed. The instruction is text that acts *on* an LLM; the only reader is the model. Apply the nine architecture-level principles in [REFERENCE.md](REFERENCE.md) (the harness→artifact map lives there too).

## Anti-patterns

- **Output longer than the change needs.** This skill writes instructions that teach brevity; a bloated rule fails its own test. Every sentence must earn its place by changing behavior.
- **Wrong artifact type for the harness.** A ChatGPT-browser user cannot install a skill or an MCP server; a Claude Code user shouldn't get a custom-instructions block when a CLAUDE.md rule is the right home. Detect the harness first; emit the form it can use.
- **Banned-phrase or symptom lists instead of properties.** Name the property that produces the unwanted behavior and let the model police itself against it. Enumerated phrases suppress one symptom and can resurface as visible output structure.
- **Skipping the proof.** A rule you haven't demonstrated is a guess. Show the model's behavior without and with the new instruction so the user sees the delta.
- **Per-rule persona injection.** Identity belongs at the global layer; a single rule shouldn't redeclare who the model is.

## The work

Run these shapes in order. Don't announce them.

### 1. Detect the environment

Establish, by asking or inspecting: OS, model + version, harness (ChatGPT browser / ChatGPT app / Claude Code / codex / Gemini CLI / other), and which config surfaces that harness exposes (custom instructions, CLAUDE.md, AGENTS.md, GEMINI.md, settings.json hooks, MCP, skills). Don't trust training-data memory of what a harness supports — **read your own documentation** (Claude Code: its docs on hooks/settings/skills/MCP; other harnesses: their current help/settings) to confirm what config surfaces actually exist before choosing one. **Read the existing config** too, before writing anything — a new rule that duplicates or contradicts what's already there is worse than none. The harness determines the artifact type; see REFERENCE.md.

### 2. Find the behavior under the request

The user names a symptom ("it's too agreeable," "it makes things up"). Find the property they actually want ("push back when it disagrees," "ground claims in the source, flag the unverified"). Write to the property, not the symptom. Gather only what you genuinely need to proceed, and only what you can't get by inspecting their setup yourself — don't gate the work behind an intake interrogation.

### 3. Draft the artifact

Write the minimal instruction in the right form for the harness, applying the nine principles: philosophy first, properties not phrases, anti-patterns with reasoning, no scripted persona. Keep it the smallest thing that produces the behavior. If it needs companion depth, reference a file rather than inlining. When the behavior is an *automated guarantee* ("always do X before claiming done"), prefer the harness's enforcement mechanism (e.g. a settings.json hook) over a model-followed rule — a hook fires deterministically; a rule relies on the model choosing to follow it. Surface that tradeoff when both are available.

### 4. Prove it

Before/after demo. Show the model responding to a representative prompt without the new instruction, then with it. The delta is the proof. If there's no visible delta, the instruction isn't doing its job — revise before shipping.

### 5. Install

Give exact steps for the detected harness — where the artifact goes, copy-paste-ready. Flag clearly what the user must paste themselves (e.g. settings the agent can't write) versus what the agent can do for them. For multi-harness users, point each harness at one shared rule set rather than maintaining copies (see REFERENCE.md, cross-CLI pointer pattern).

## Quality the output should have

- It is the smallest instruction that produces the intended behavior.
- The artifact type matches a config surface the user's harness actually exposes (confirmed against the harness's own documentation, not assumed).
- It states properties, not banned phrases.
- It neither duplicates nor contradicts the user's existing config (which was read first).
- It ships with a before/after proof the user can see.
- Install steps are exact for the detected harness, with paste-yourself vs agent-does-it called out.
