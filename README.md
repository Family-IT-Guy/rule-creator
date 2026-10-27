# rule-creator

You want your AI to start doing something, or stop. Push back when it disagrees instead of caving. Ground its claims in the source. Stop ending every answer with a recap. `rule-creator` turns that wish into the smallest instruction that changes the behavior, in the form your setup can use, and tells you how you'll know whether it worked. It also audits the rules you already have.

It's a skill from Family IT Guy, and it works with any AI model and any tool you use it in.

## How it writes a rule

Ask most models for a system prompt and you get a list of banned phrases, which suppresses one symptom until it comes back in other words. `rule-creator` starts from the behavior instead.

- It finds the behavior under the request. You name a symptom ("it's too agreeable") and it writes to what you want ("push back when it disagrees").
- It ties the rule to a moment. A rule like "don't do X" or "be honest" rarely fires, because nothing in the work reminds the model of it. `rule-creator` writes "when this happens, do that", naming something the model will see at the time, such as a word in a file, a point in a task or a claim it's about to make.
- When there's no moment to tie a rule to, it checks whether a permission, a setting or a hook in your tool can enforce the behavior instead. When neither a rule nor the tool can guarantee it, it says so.
- It picks the form your setup reads. A chat app gets a block for its custom instructions. A tool that works on your files gets a line in its instruction file, a skill, or a hook in its settings. It reads your existing setup first, so the new rule doesn't repeat or contradict what's there.
- It carries the reason. Each rule says in one sentence what goes wrong without it, which lets the model handle cases the rule never lists.
- It tells you how to check the rule. Usually that means what to look for in your next few sessions. When a mistake can't be undone or reaches other people, it runs the same prompts with and without the rule, several times each, and reports how many changed. A single before-and-after run doesn't count as proof.

Then it gives you exact install steps, separating what you have to paste yourself from what it can do for you.

## Auditing the rules you have

Point it at your instruction files, rules or skills and it reads them as a set. It looks for rules that say the same thing in two places, rules that give opposite instructions for the same moment, rules pointing at files that no longer exist, and rules with no moment at all. It proposes changes and removals and leaves the deleting to you.

## When to reach for it

- Your AI keeps doing something you've asked it to stop, and reminders don't stick.
- You're writing or fixing a rule in an instruction file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` or your tool's own), a skill or a custom-instructions block, and want it to hold.
- Your rules have piled up and you want to know which ones do anything.

## Install

The skill is the folder `skills/rule-creator`: `SKILL.md` and `REFERENCE.md`.

- If your tool loads skills, put that folder where it looks for them.
- If it doesn't, give your AI both files and ask it to follow them when you want a rule written or checked.
- The setup prompt in [ai-instruction-router](https://github.com/Family-IT-Guy/ai-instruction-router) can install it for you in whatever tool you use.

In Claude Code you can install it from the Family IT Guy plugin marketplace instead:

```
/plugin marketplace add Family-IT-Guy/plugins
/plugin install rule-creator@familyitguy
```

Then ask for a rule in plain words ("write me a rule so my AI stops…"). If Claude Code asks permission to read the plugin's reference file, allow it. To update later: `/plugin marketplace update familyitguy` then `/plugin install rule-creator@familyitguy`.
