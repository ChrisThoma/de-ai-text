# de-ai-text

An agent skill for [Claude Code](https://claude.com/claude-code) that audits and cleans text so it reads human-written, not machine-generated.

It uses Wikipedia's ["Signs of AI writing"](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) as its reference for what to look for: em-dash overuse, curly quotes, the AI-vocabulary cluster ("delve," "tapestry," "leverage," "robust"...), "not just X but Y" constructions, puffery, didactic disclaimers, manufactured triads, and more. The skill scans with grep recipes, judges each hit against legitimate domain usage, fixes the genuine tells without flattening the author's voice, and verifies with a re-scan.

The goal is "reads human," not "passes a detector." The skill is explicit about the two failure modes: shipping obvious tells, and over-correcting into bland mush.

## Install

With the [skills CLI](https://skills.sh) (works for Claude Code, Cursor, Codex, and others):

```bash
npx skills add ChrisThoma/de-ai-text
```

Or clone directly into your Claude Code skills directory:

```bash
git clone https://github.com/ChrisThoma/de-ai-text.git ~/.claude/skills/de-ai-text
```

Or for a single project, clone into `.claude/skills/de-ai-text` inside the repo.

## Use

In Claude Code, invoke it directly:

```
/de-ai-text path/to/file-or-dir
```

Or just ask in natural language; the skill triggers on phrases like "de-AI this," "remove AI tells," "does this sound like AI," "de-slop," or "make this read human."

## What it does

1. **Scan** the target with grep recipes; get counts, not vibes.
2. **Judge** each hit: genuine tell, or legitimate domain usage? ("Leverage" as a bargaining chip stays; "leverage synergies" goes.)
3. **Fix** with meaning-preserving conventions (em-dash asides become parentheses, clause joins become semicolons, AI-vocabulary words become the plain word that means it).
4. **Verify** by re-scanning to zero and re-reading passages aloud.
5. **Report** counts before and after, what changed, and what was deliberately kept.

See [SKILL.md](SKILL.md) for the full method.

## License

[MIT](LICENSE)
