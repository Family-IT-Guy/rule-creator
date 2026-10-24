# rule-creator

You want your AI to start doing something, or stop. Push back when it disagrees instead of caving. Ground its claims in the source. Stop ending every answer with a recap. `rule-creator` turns that wish into the smallest instruction that changes the behavior, in the form your setup can use, and tells you how you'll know whether it worked. It also audits the rules you already have.

It's a Claude Code plugin from Family IT Guy.

## How it writes a rule

Ask most models for a system prompt and you get a list of banned phrases, which suppresses one symptom until it comes back in other words. `rule-creator` starts from the behavior instead.

- It finds the behavior under the request. You name a symptom ("it's too agreeable") and it writes to what you want ("push back when it disagrees").
- It ties the rule to a moment. A rule like "don't do X" or "be honest" rarely fires, because nothing in the work reminds the model of it. `rule-creator` writes "when this happens, do that", naming something the model will see at the time, such as a word in a file, a point in a task or a claim it's about to make.
- When there's no moment to tie a rule to, it checks whether a permission, a setting or a hook in your tool can enforce the behavior instead. When neither a rule nor the tool can guarantee it, it says so.
- It picks the form your setup reads. A ChatGPT user gets a custom-instructions block. A Claude Code user gets a `CLAUDE.md` rule, a skill or a `settings.json` hook. It reads your existing config first, so the new rule doesn't repeat or contradict what's there.
- It carries the reason. Each rule says in one sentence what goes wrong without it, which lets the model handle cases the rule never lists.
- It tells you how to check the rule. Usually that means what to look for in your next few sessions. When a mistake can't be undone or reaches other people, it runs the same prompts with and without the rule, several times each, and reports how many changed. A single before-and-after run doesn't count as proof.

Then it gives you exact install steps, separating what you have to paste yourself from what it can do for you.

## Auditing the rules you have

Point it at your `CLAUDE.md`, rules folder or skills and it reads them as a set. It looks for rules that say the same thing in two places, rules that give opposite instructions for the same moment, rules pointing at files that no longer exist, and rules with no moment at all. It proposes changes and removals and leaves the deleting to you.

## When to reach for it

- Your AI keeps doing something you've asked it to stop, and reminders don't stick.
- You're writing or fixing a `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` rule, a skill or a custom-instructions block and want it to hold.
- Your rules have piled up and you want to know which ones do anything.

## Install

```
/plugin marketplace add Family-IT-Guy/plugins
/plugin install rule-creator@familyitguy
```

Restart Claude Code, then ask for a rule in plain words ("write me a rule so Claude stops…"), or type `/rule-creator:rule-creator`. If Claude Code asks permission to read the plugin's reference file, allow it.

To update later: `/plugin marketplace update familyitguy` then `/plugin install rule-creator@familyitguy`. (Or turn on auto-update in the `/plugin` menu under Marketplaces.)
