---
name: rule-creator
description: Write, audit, or remove an instruction that changes how an LLM behaves — a custom-instructions block, a CLAUDE.md/AGENTS.md/GEMINI.md rule, or a skill — fitted to the user's harness and tested against its own absence. Use when the user wants their AI to start or stop doing something, asks to write or fix a rule or system prompt, asks why a rule isn't working, or wants existing rules audited, thinned, or removed.
---

# Rule Creator

Produce the smallest instruction that reliably changes the model's behavior, in the form the user's harness can actually use — and test it against its own absence. The only reader is the model. Where each kind of instruction goes, and the nine architecture principles: [REFERENCE.md](REFERENCE.md). If a `personal.md` sits beside this file, read it first: it holds the owner's own paths and cases, which override the defaults below.

Two paths: **write** a new rule → steps 1–7. **Audit** existing rules → step 8. Every step ends on a **Done when** check; a step without one gets skipped under the pull of the next.

## The shape test — every rule, new or existing, passes this first

**Does the rule name a moment?** A rule shaped as *don't do X* or *value Y* fires only when the model runs a deliberate check, and generation rarely runs one — so it stays true and unreached. Tested twice against a no-rule control; zero measurable effect both times. Shape is the failure, not length.

Three outcomes, in order of preference:

1. **A moment exists** → write it as *when T, do A*. The trigger must be concretely present at the moment of need: a word in an artifact, a named point in a task, a file being touched, a claim about to be made. "When relevant" is not a moment.
2. **No moment, but the harness can enforce it** → use the harness, and decide first whether the mechanism will BLOCK or only REPORT, because that decides what it is allowed to check. A blocking mechanism needs a failure set enumerable **completely** in advance; over an open set a blocking check is worse than nothing, since it misses every novel case while producing the confidence that a check ran. A reporting mechanism carries no such requirement — an open set is fine, provided a miss leaves things exactly as they are today and a false positive costs one line of reading. Neither of those errors produces false confidence, which is the harm being avoided. Blocking and cannot enumerate → back to 1, or 3, or make it report instead. Check the model-independent surface in cost order — a declarative permission entry first, then a settings change, then a hook, which is general-purpose and the most expensive. And it logs every fire from its first commit, or it is not observable and cannot later be removed.
3. **Neither** → say so inside the rule. It is a discipline that holds only while the user corrects lapses, not a gate. Never dress it as a mechanism; a rule claiming something happens automatically must name the hook, script, or harness event that causes it, in the same sentence.

## Anti-patterns

- **Claiming proof from a single run.** Model behavior is non-deterministic — identical prompts and identical configs produce different outcomes run to run. One before/after demo is an illustration, never evidence.
- **Adding without a removal path.** A capture loop with no inverse can only grow, and growth then becomes indistinguishable from improvement because nothing measures either.
- **Banned-phrase or symptom lists instead of properties.** Name the property that produces the behavior. Exception: where the unwanted output is a *closed, known* set of literal strings, a phrase check belongs in a hook, not in the rule text.
- **Wrong artifact for the harness.** Detect it first; emit the form it can use.
- **Per-rule persona injection.** Identity belongs at the global layer.

## The work

Run these in order. Don't announce them.

### 1. Detect the environment

OS, model + version, harness, and its config surfaces. **Read the harness's own current documentation**, not training-data memory of it. **Read the existing config first** — a rule that duplicates or contradicts what is there is worse than none. List the rules and skills that already touch the same moment.

Done when you can name the harness and its config surfaces from its current documentation, and name each overlap with the existing config, or say there are none.

### 2. Find the behavior under the request

The user names a symptom ("it's too agreeable"). Find the property they want ("push back when it disagrees"). Write to the property. Gather only what you cannot get by inspecting their setup.

**Capture the author's own words before drafting, then distil with their agreement.** Ask what they are trying to accomplish and keep the answer verbatim — loose phrasing included — with the date and session id. The verbatim is the record; a one-sentence distillation the author signs off on is what the rule carries. Keep both, because a stated reason nobody can check against the original is how a rule drifts from what the author was trying to accomplish. Where the author cannot say what they wanted, record that rather than inventing one. Where no author is present because the rule was proposed off an observed correction, say so in the record and mark the reason as inferred. Destination: a `rule-intents.yml` kept with the user's other state files (next to their global instructions if they keep none), created if absent — never anywhere the harness loads into every session, such as a rules folder, which would defeat the point of keeping it out of the rule. Editing an existing rule? Update its entry in the same turn — the file/section pointer and, where the wording changed, the agreed distillation.

Done when the author's words are recorded verbatim and the author has agreed the one-sentence version, or the record says the reason is inferred.

### 3. Find the moment

Apply the shape test above before drafting a word. This is the step that decides whether you are writing a rule at all, or building a hook, or telling the user plainly that this one rests on correction. Most rules that fail in practice failed here.

Done when the rule is written as *when T, do A*, assigned to a named harness mechanism with block or report decided, or labeled as holding only through the user's corrections.

### 4. Draft it

The smallest instruction producing the behavior, in the harness's form. State the trigger inline; push depth into a companion file reached by a pointer rather than inlining it. Carry the reason in the rule, in one sentence, and only the author's — naming the failure and what it costs is what lets the model handle variants the rule never enumerated, and is usually what prevents the next three "do NOT" patches, so the reason is what makes a rule shorter rather than an addition to it. **The reason has to be resolvable where the rule sits.** A reason that cites another document cannot be acted on without fetching it, and where the citation is a rule that was retired, fetching it puts withdrawn instructions into the context window as though they were live. State the failure and its cost in the rule's own words; cite nothing. A reason you cannot source to the author is the failure mode: it reads as authoritative and steers toward a purpose nobody has. Where a rule is irreducible personal preference — how the user wants things worded, sequenced, framed — no model improvement derives it, so do not thin it on the argument that newer models need fewer rules. That argument applies to invariants and to compensations for tools that fail silently, not to taste.

**What does not go in the rule.** Operational meta — who ruled it and when, dates, trial status, revert paths, where something moved from — is read rarely and only by maintenance, while the rule itself is re-sent on every request of every session. Route it to the intents record or the decisions log. One exception, and it is content rather than meta: the specific failure the rule exists to prevent. Naming the incident is what makes a session treat a hard rule as binding, so a safety rule keeps its incident and loses its attribution.

Done when the draft is in its final wording for its home, carries the author's reason in its own words (or is marked inferred), and holds no operational meta.

### 5. Test it against its own absence

For a reversible rule, install it and check it in real use. Name the behavior you will look for in real sessions, and when: which sessions, and how long after install. Reading those sessions for that behavior is the test.

Run a with/without test before install only when the rule guards one of three cases, or when its behavior cannot be seen in use. The three cases are: the mistake cannot be undone (deleting, sending, money, a production system others rely on); the result reaches someone outside (customers, the public, anything with the user's name on it); use would not show the failure (backups, security fences, data written where nobody looks).

Where a with/without test runs, run the same representative prompts with and without the instruction, several times each, and report how many runs changed. A single pair is an illustration. If the rule shows no delta across repeats, it is not doing its job — revise or drop it before shipping. Where repeats are impractical, say the result is unproven rather than presenting one run as a demonstration.

**Label which of two experiments you ran; they answer different questions.** *Foregrounded* — the candidate sits in the prompt, guaranteed read — measures whether the wording changes behaviour once reached, and says nothing about reach. *Harness-loaded* — the rule sits where it will actually live, inside its full file, with a competing task running — is the only one that speaks to whether it fires at all. A rule can pass the first and never fire in practice, which is the common failure for always-loaded rules and is invisible to a foregrounded test. Report the label with the result, and where only the foregrounded test was run, say the rule's firing is untested rather than letting a wording result stand as evidence about behaviour.

Run with/without test sessions only where they cannot act: a sandboxed or read-only permission mode, no issue-tracker or other account tools, and a working folder that holds no live state. Check that one run's actions are refused before starting the rest. Test runs told only to read have posted to an issue tracker and overwritten a live session's notes.

Done when you have named what to look for, in which sessions and when, or reported how many runs changed out of how many, with and without the rule, under its experiment label.

### 6. Present it for approval

Show the exact text, its home and why, each overlap it touches with the edits or removals that overlap needs, and how you will know it worked. Write nothing until the user approves, because a rule shapes every session after it and the user decides what their AI believes.

Done when the user has approved the exact text and home, or asked for changes.

### 7. Install

Exact steps for the detected harness, copy-paste ready. Flag what the user must paste themselves versus what the agent can write. For multi-harness users, point each harness at one shared rule set rather than maintaining copies. Apply the approved overlap edits in the same pass.

Done when the rule reads back from its home exactly as approved, its intents entry points at it, and every approved overlap edit is applied.

### 8. Audit an existing set

Read the rules rather than classifying them mechanically — for a set small enough to read, reading is the correct first method and a measurement is what needs justifying. A search over prose can show a phrase is absent, never that a concept is; a hit is real evidence, which is exactly why a miss feels like evidence and is not. Claims about what a rule argues come from reading it.

**Define the set before reading it, and put the rule that INVOKES the material inside it.** A defect in a set of instructions lives as often between two of them as inside one, and the instruction that tells the model to load them is a member: if it names a file that moved, a skill the harness will not hand over, or two sources that disagree, no per-file reading finds it. Where the user keeps an index of their instructions, start from it rather than re-deriving one — the `rule-intents.yml` from step 2 is such an index, one entry per rule with its file, section and basis.

**Enumerate the set by command, never from memory of what you have open.** Two searches, both run: grep every always-loaded file for the name of each member, which finds the invocation sites; and list every skill the harness serves whose description fires on the same moment, which finds the competitors. A set assembled from the files already open is the files already open. An audit that skipped both searches missed a sixth member and three of four invocation sites.

**Two passes, and the first cannot be done inside the second.**

**Pass A, over the whole set at once.** These are relations, not properties, so a rule-by-rule loop answers them against whatever the reader still happens to be holding — which is how duplicates survive audits.

**Pass A is done when, and not before:** every member has been compared against every other member, and you can say how many pairs that was; every pointer in every member has been resolved by running the resolution, not by reading the pointer; and every member has been invoked. Report the counts. An unbounded check is one that stops when the reader tires, and a set-level check with no count is indistinguishable from a per-file loop with a set-level heading.

- **Duplication** — is a meaning stated in more than one place? Two copies drift; edit one and the other silently contradicts it. Keep one canonical home. Legitimate layering is not duplication: a trigger plus its detailed method, or a principle restated as an enforcement checkpoint in a different context.
- **Contradiction** — do two members give different instructions for one moment? Competing mandates teach the model that mandates of that kind are not binding, discounting every other one. Check across layers too: a skill demonstrating what a global rule forbids is this defect.
- **Reachability** — can the model actually get to each member at the moment the set says to use it? Invoke it and see. An instruction pointing at something the harness refuses to serve is dead in a way that reading the text never reveals.

**Pass B, rule by rule.**

- **Shape** — does it name a moment? Apply the shape test. Report which rules are values with no trigger and no mechanism; that set is the finding.
- **Dangling references** — does it point at a file, script, or rule that exists?
- **Provenance** — can you state what the author wanted, in their words? If not, that is a finding. Search the user's past conversations, where they keep them, before asking: a rule can predate its git history and the author may not remember, and a conversation record can often answer what git cannot. Not grounds for removal.
- **Removal** — three findings each justify proposing removal: the rule fired and the work would have gone the same way without it; the situation it was written for no longer exists; another rule now covers it. **That a rule has never fired is NOT grounds** — a rule that silently broke and one whose case never arose are indistinguishable from outside, so removing on non-firing removes the broken ones preferentially.

Propose; never remove unilaterally. The user rules on each.

Done when Pass A's counts are reported, every member has a Pass B finding on each of the four checks, and every proposed change is in front of the user.
