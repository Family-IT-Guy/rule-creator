# rule-creator

You want your AI to start doing something — or stop. Push back when it disagrees
instead of caving. Ground its claims in the source. Stop padding every answer with
caveats. `rule-creator` turns that wish into the smallest instruction that actually
changes the behavior, in the form your setup can use, and proves it changed with a
before/after demo.

It's a Claude Code plugin from Family IT Guy.

## What it does differently

Most "write me a system prompt" attempts produce a wall of banned phrases that
suppress one symptom and resurface as something else. `rule-creator` works the other
way:

- **Finds the behavior under the request.** You name a symptom ("it's too agreeable");
  it writes to the property you actually want ("push back when it disagrees").
- **Picks the right artifact for your harness.** A ChatGPT-browser user gets a
  custom-instructions block; a Claude Code user gets a `CLAUDE.md` rule, a skill, or a
  `settings.json` hook — whichever the harness can actually use. It reads your existing
  config first so the new rule doesn't duplicate or contradict what's there.
- **States properties, not phrase blacklists.** It names what produces the unwanted
  behavior and lets the model police itself, so the fix generalizes instead of playing
  whack-a-mole.
- **Proves it.** Before/after demo on a representative prompt. If there's no visible
  delta, the rule isn't doing its job — and it tells you.

Then it gives you exact install steps for your harness, with what you have to paste
yourself called out separately from what it can do for you.

## When to reach for it

- Your AI keeps doing something you've asked it to stop, and reminders don't stick.
- You're writing or fixing a `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` rule, a skill, or a
  custom-instructions block and want it to actually hold.
- You want a behavior change to be an enforced guarantee, not a suggestion the model
  may ignore.

## Install

```
/plugin marketplace add Family-IT-Guy/plugins
/plugin install rule-creator@familyitguy
```

To update later: `/plugin marketplace update familyitguy` then
`/plugin install rule-creator@familyitguy`. (Or turn on auto-update in the `/plugin`
menu under Marketplaces.)
