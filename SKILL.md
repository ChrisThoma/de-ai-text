---
name: de-ai-text
description: Use to audit and clean text so it reads human-written, not machine-generated. Covers what to look for (guided by Wikipedia's "Signs of AI writing"), how to scan for the markers, how to fix them without flattening a good voice, and how to verify. Works on any prose in any project (course text, docs, articles, READMEs). Trigger phrases: "de-AI this", "remove AI tells", "does this sound like AI", "AI speak", "de-slop", "make this read human", "check for signs of AI writing".
---

# De-AI text

Make text read as if a knowledgeable human wrote it, by finding and removing the patterns that mark machine-generated prose, without changing meaning and without flattening a voice that is already good.

The reference for *what to look for* is Wikipedia's **"Signs of AI writing"**: <https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing>. Re-fetch it when you need the current, full list; the high-signal subset is captured below so a routine pass needs no fetch.

**The goal is "reads human," not "passes a detector."** Two failure modes to avoid: (1) shipping obvious tells (em-dashes, puffery, "not just X but Y"); (2) over-correcting so hard that you strip the intentional voice, the real domain triads, and the second-person teaching tone, leaving bland mush. Both read as machine output. Aim for the middle: surgical removal of tells, voice preserved.

## Method (in order)

1. **Scan** the target for markers (use the grep recipes below). Get counts, not vibes.
2. **Judge** each hit: is it a genuine tell, or legitimate domain usage in disguise? Most false positives come from skipping this step. See "Tell vs. legitimate" below.
3. **Fix** the genuine tells with the conventions below, preserving meaning and rhythm.
4. **Verify**: re-scan to zero on the high-signal markers, re-read a few passages aloud, and re-validate anything structured you touched (JSON, links, code).
5. **Report** honestly: counts before/after, what you changed, and what you deliberately left and why. If it was already clean, say so plainly. Do not manufacture edits to look busy.

## What to look for (the markers)

Ordered by signal strength. The top group is where almost all real "AI smell" lives; clear these first.

### High-signal (almost always a tell)
- **Em-dashes (—)** used for asides or emphasis. The single strongest tell. (Keep en-dashes `–` in ranges, arrows `→ ↔ ←`, and box-drawing.)
- **Curly quotes and apostrophes** (`' ' " "`) where the surrounding text uses straight `'` `"`.
- **"Not just X, but (also) Y"** and the negative-then-positive reveal **"It's not X, it's Y"** when vague. Concrete contrasts ("not a gotcha; a puzzle") are fine; vague reveals ("not just a tool, it's a mindset") are the tell.
- **AI-vocabulary cluster**: delve, tapestry, testament, boasts, vibrant, pivotal, crucial, underscore, meticulous, intricate/intricacies, foster, showcase, leverage (verb), robust, seamless, landscape, realm, nestled, renowned, groundbreaking, game-changer, cutting-edge.
- **Didactic disclaimers**: "it's worth noting," "it's important to note," "in conclusion," "in summary," "needless to say."
- **Puffery / weasel attribution**: "stands as a testament," "plays a vital/crucial role," "rich cultural/history," "experts say," "studies show," "in today's fast-paced world."
- **Emoji as decoration** (📝 🔥 ✅). Functional arrows in diagrams are not this.

### Structural (a tell only when overused)
- **Rule of three**: manufactured parallel triples for rhythm. Real domain triads (kick/snare/hat; the three pillars) are fine; the tell is inventing triples where the content has no three.
- **"From X to Y"** sweeps ("from ancient times to the modern day"). Literal ranges ("from town to the ruin") are fine.
- **Bold-lead-in vertical lists** (`- **Header:** text`). A legitimate, common format. Only a tell in aggregate when *every* paragraph is one, and many house styles use it on purpose; do not strip it reflexively.
- **Elegant variation**: swapping synonyms for the same concept across sentences to avoid repetition. Prefer calling the same thing the same name.
- **Present-participle tack-ons** for false depth: "..., highlighting the importance of...", "..., ensuring...", "..., reflecting a broader...".

### Voice (encyclopedia tells; weigh against the format)
- **Collaborative meta-narration**: "In this section we will explore," "let us examine." Note: direct second-person instruction ("you set the value," "we cover this next") is correct and expected in a tutorial, course, or guide; do not remove it. The tell is empty tour-guide narration, not teaching.
- **Knowledge-cutoff or hedging disclaimers**: "as of my last update," speculation about gaps.
- **Signposting headings** like "Looking ahead" are fine as genuine transitions; flag them only if they front empty filler.

## Tell vs. legitimate (judge before you cut)

The most common mistake is flagging domain language. Examples seen in practice:
- **"leverage"** as a bargaining chip ("with leverage, it's a roll at disadvantage") or **"high-leverage"** meaning high-impact: legitimate. The tell is "leverage" the verb in the synergy sense.
- **"Empower," "Craft," "Harvest"**: legitimate when they are proper names of mechanics, tools, commands, or APIs.
- **Real triads** that map to actual content (three roles, three pillars, K-N-P): keep.
- **En-dashes in ranges** (`Days 1-7`, `f/5.6-f/8`), **arrows** (`→`), **ASCII diagrams**: keep.
- **Code** (comments, error strings): out of scope unless asked; clean separately.

When unsure whether a word is a tell, read the sentence: if removing or swapping it changes a concrete meaning, it was carrying weight and is probably legitimate.

## Scan recipes

Set the target, then run. These print matches so you can judge.

```bash
TARGET=path/to/dir-or-file      # a dir, a file, or "."

# 1. Em-dashes in prose (excluding arrows/box-drawing)
grep -rn -- "—" "$TARGET" | grep -v "→\|↔\|←" | grep -vP "[─━│┌┐└┘├┤┬┴┼]"

# 2. Curly quotes / apostrophes
grep -rnP "[\x{2018}\x{2019}\x{201C}\x{201D}]" "$TARGET"

# 3. AI-vocabulary cluster
grep -rniE "delve|tapestr|testament|boasts|vibrant|pivotal|crucial|underscore|meticulous|intricat|foster|showcas|leverag|robust|seamless|nestled|renowned|groundbreaking|game[- ]chang|cutting[- ]edge|in today'?s|fast[- ]paced|navigat(e|ing) the (complex|landscape)" "$TARGET"

# 4. "not just X but Y" and vague reveals
grep -rniE "not (just|only|merely)[^.]*\bbut\b" "$TARGET"

# 5. Didactic / meta disclaimers
grep -rniE "worth noting|important to note|in conclusion|in summary|needless to say|that said|when it comes to" "$TARGET"

# 6. Puffery / weasel attribution
grep -rniE "stands as|a testament|vital role|crucial role|rich (history|tradition|tapestry|cultural)|experts (say|argue|agree)|studies show|in the realm of" "$TARGET"

# 7. Emoji (excluding intended decorative marks like ★ and functional arrows)
grep -rnP "[\x{1F000}-\x{1FAFF}\x{2600}-\x{27BF}]" "$TARGET" | grep -v "★\|→\|↔\|←"
```

For the structural patterns (rule of three, bold-list density, "X, not Y"), eyeball rather than gate on a count; they are voice, not defects.

## Fix conventions

Preserve meaning; vary the replacement so you do not turn every dash into a colon.

- **Em-dash for an aside (paired `— ... —`)** → parentheses.
- **Em-dash joining two independent clauses** → semicolon, or split into two sentences.
- **Em-dash before a summary/expansion** → colon.
- **Em-dash for a quick clause break** → comma.
- **`# Heading — Subtitle`** → `:`; if the heading already has a colon, wrap the subtitle in parens.
- **`**Bold lead** — explanation`** → `**Bold lead:** explanation`.
- **Rubric/label `**1 — Novice:**`** → `**1 (Novice):**`.
- **Curly quotes** → straight `'` `"`.
- **AI-vocabulary word** → the plain word that means it ("use," "key," "strong," "smooth," "important"), or delete the sentence if it carried nothing.
- **"Not just X, but Y"** → state Y directly, or "X, and also Y," or keep the contrast only if it is concrete.
- **Didactic disclaimer** → delete the lead-in and keep the fact.
- **Manufactured triad** → cut to the two that matter, or add real content for the third.

**Bulk em-dash pass (many files):** grep the dashes, then write a small script mapping exact `old -> new` string pairs per file (`.replace` is global, so duplicates map fine), plus a `globals` list for repeating patterns like `("** — ", "**: ")`. Run the specific paired-aside cases *before* the globals so `** — ` asides get consumed first. Print a MISS for any pair that did not match, then re-grep to confirm zero. Watch sentence-initial capitalization when a clause break becomes a sentence break.

## Verify before declaring done

- Re-run scans 1-5 above; high-signal markers should be zero (or only intended diagram lines).
- If you touched anything structured, re-validate it (JSON parses, links resolve, code still runs).
- Read two or three passages aloud. If a sentence sounds like marketing copy or a press release, rewrite it.
- Confirm you did not delete real content, real domain terms, or the teaching/author voice while removing tells.

## Report

State the outcome plainly: marker counts (before, after), the specific changes made, and anything left on purpose with the reason (for example, "9 concrete negative-parallelism contrasts kept as intentional voice"). If the text was already clean, report that and stop. Do not over-edit a voice that is already human just to show activity.

## Adapting to a project's conventions

This skill is general; honor each project's house style when it has one:
- Check for a repo `CLAUDE.md`, a style guide, or an authoring skill that states the house voice, and defer to it. (For example, if a project has an authoring skill that carries its own voice rules, this skill is the auditing counterpart; keep the two consistent.)
- Decide per project what counts as intended voice versus a tell. Bold-lead-in lists, triads, and second-person address all vary by house style.
- Prefer writing new text born-clean over cleaning it after the fact.
