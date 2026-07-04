---
name: post-extractor
description: Processes source material — URLs, X/Twitter threads, blog posts, articles, PDFs, YouTube videos, newsletters, GitHub repos, podcast transcripts, or raw pasted text — into a single Obsidian-ready markdown note with full Dataview-friendly frontmatter, separating factual claims from marketing language, flagging testable assertions, and surfacing skepticism. Handles single sources and batch inputs. Activates when the user pastes a link, pastes raw content, uploads a file, or says "extract this," "process this post," "make a note from this," "run this through the filter," or uploads content for vault ingestion. Activates on any input that is clearly source material rather than a question or task.
---

# Post Extractor Skill

## Purpose

Turn raw inputs — URLs, X threads, blog posts, articles, research notes — into clean Obsidian notes that go straight into `00_inbox/`. The skill enforces the skepticism-filter discipline: factual claims separated from marketing language, testable assertions flagged, source credibility surfaced rather than hidden.

The user's vault is the source of truth. Skills are the compiled output. This skill produces the raw material the vault accumulates from.

## When this skill activates

Activate on any of:

- A URL pasted with intent to ingest ("extract this," "process this," or just a bare URL with context suggesting capture)
- Raw text pasted that is clearly source material (X thread, blog post, article body, transcript)
- User says: "make a note from this," "run this through the filter," "skepticism filter this," "add to inbox"
- Uploaded `.md`, `.txt`, `.html`, or `.pdf` file that is clearly external content rather than a vault note
- Multiple URLs or sources pasted at once (batch — see §16)
- A YouTube or other video URL (see §17)

Do NOT activate on:

- Quick lookups or questions about a link ("what does this article say?")
- Code the user is sharing for review
- Files matching other skill triggers (chat summaries, project docs, etc.)

## Triage before extraction

Every post gets classified before producing a note. State the classification in a 1-2 sentence acknowledgment before producing output.

Classifications:

- **primary source** — full extraction
- **pointer-to-primary** (X post linking to GitHub repo, paper, or article) — fetch the target, extract that, mention pointer post only as the pointer
- **content-factory template** — stub
- **accurate-but-derivative** (real content but paraphrased from better primary sources) — stub
- **real-product, fabricated-specifics** (real product wrapped in invented dates/numbers/percentages) — stub
- **fabricated-feature listicle** (format mimics documentation/feature-lists; items presented in visual grammar of `/help` or product changelog; significant fraction invented or misattributed) — stub
- **tool-promotion** (real subject as credibility hook, named tool as CTA, affiliate funnel) — stub
- **ambiguous** — ask

For identified archetypes and pattern catalog, see `../references/archetypes.md`.

**One source, one note — with exception.** When a single post points at multiple distinct primary sources, produce separate notes per primary source plus a stub for the pointer.

## Inputs

The skill handles these input modes:

**URL mode** — user pastes a link. Use `web_fetch` to retrieve the content. If fetch fails or returns insufficient content, surface that explicitly (fail loud) and ask whether to proceed with what was retrieved or to have the user paste the raw text.

**Raw paste mode** — user pastes the content directly. Work from what's provided.

**Uploaded file mode** — user uploads a `.md`, `.txt`, `.html`, `.pdf`, or similar file. Read the file from `/mnt/user-data/uploads/`. For PDF files, see §18.

**Batch mode** — user pastes multiple URLs or sources at once. See §16.

**YouTube / video URL mode** — user pastes a YouTube or other video URL. See §17.

In all modes, ask for the source URL if it isn't obvious and the content makes claims that need attribution.

## Output

A single fenced markdown code block containing the complete note. The user copies and saves to their Obsidian vault under `00_inbox/`. Filename convention: `YYYY-MM-DD-short-slug.md`. State the suggested filename above the code block.

### Frontmatter (required, exact field set)

```yaml
---
title: "Concise descriptive title"
source: "https://full.url.here"
source_type: article | thread | blog | paper | video-transcript | podcast-transcript | pdf | github-repo | newsletter | interview | other
author: "Author or handle, or 'unknown'"
date_published: YYYY-MM-DD  # or 'unknown'
date_captured: YYYY-MM-DD
status: inbox
confidence: high | medium | low
confidence_note: "One line on why this credibility rating"
domain: trading | acquisition | infra | ai | other
tags: [tag-one, tag-two, tag-three]
testable: true | false
---
```

Field rules:

- `status: inbox` always on capture. Gets manually promoted later.
- `confidence` is about the *source's credibility*, not extraction accuracy. See `../references/quality-signals.md` for the diagnostic checklist that informs this rating.
- `domain` is a single value, not a list — forces a primary categorization. Use `other` if genuinely cross-domain.
- `tags` are kebab-case, no `#` prefix in the YAML array. 3-7 tags. Specific over generic (`funding-rate-arbitrage` not `crypto`).
- `testable: true` if the post makes claims that could be checked with data, code, or an experiment. This is the field Dataview queries will hit when the user wants "show me everything I should validate."

### Body structure (in this exact order)

```markdown
# {title}

## Source

- **URL:** {source}
- **Author:** {author}
- **Captured:** {date_captured}

## TL;DR

One paragraph, 3-5 sentences. What is this post saying, in plain language. No editorializing yet.

## Key Claims

Factual claims the post makes, as a list. Each claim is one sentence. Use direct quotes ONLY where the exact wording carries meaning (under 15 words, in quotation marks with attribution). Otherwise paraphrase.

- Claim 1
- Claim 2
- Claim 3

## Testable Assertions

Claims that could be empirically validated. Each entry: the claim, and a brief note on how it could be tested. Skip this section entirely if `testable: false`.

- **{claim}** — could be tested by: {method}

## Marketing / Promotional Language

Anything that reads as sales, hype, or unsubstantiated assertion. Quote briefly (under 15 words) where useful. This section is the skepticism filter made visible.

- {flagged phrase or pattern}

## My Flags

Notes from extraction. Things that pattern-match to known failure modes. Internal contradictions. Missing evidence. Suspiciously round numbers. Anything that warrants follow-up.

- {flag}

## Open Questions

Things the post raises but doesn't answer. Things a future-you might want to dig into.

- [ ] Question 1
- [ ] Question 2

## Related (optional)

Links to other vault notes if obvious connections exist. Use `[[wiki-link]]` syntax. Leave empty if nothing obvious.

**Body-embedded links count.** If a note has functional `[[wiki-links]]` in its body (e.g. in a "What's actually true" or "What to read instead" section), those count as Related links for Obsidian's graph/backlinks. Do not add a duplicate `## Related` section just for structural uniformity — only patch when a genuinely missing link exists.

-
```

### Stub format (for non-primary classifications)

When the triage classification is anything other than `primary source`, produce a stub instead of a full extraction:

- Same frontmatter as a full extraction.
- `confidence: low` with `confidence_note` naming the specific failure mode (e.g. "fabricated-specifics", "derivative paraphrase of <upstream source>", "hosted-API install funnel").
- Body sections: **Source**, **TL;DR**, **My Flags** only — skip Key Claims, Testable Assertions, Marketing Language, Open Questions.
- When slop wraps a real subject, add a **"What's actually true"** section (for vault context) and a **"What to read instead"** section pointing at better primary sources. These earn the stub its keep.

## Output format

After processing, respond with:

1. Brief acknowledgment (1-2 sentences) of what was processed
2. Suggested filename on its own line
3. Fenced markdown code block containing the full note

Example response shape:

```
Processed {what}. Confidence: {level}. Flagged {N} concerns.

Suggested filename: `2026-05-24-example-slug.md`

```markdown
---
[frontmatter]
---

[body]
```
```

## What NOT to do

- Do not write a note longer than the source. Distillation, not expansion.
- Do not editorialize in TL;DR or Key Claims. Save opinions for My Flags.
- Do not skip sections to be tidy. Empty sections get "None identified" or "Nothing flagged."
- Do not invent confidence — if you genuinely can't assess source credibility, write `medium` with `confidence_note: "Unable to assess — source context unclear"`.
- Do not produce multiple files at once unless the user explicitly says so. One post, one note.
- Do not auto-promote to other vault folders. Output is always `status: inbox`.

## Length discipline

A typical note runs 150-400 lines of markdown including frontmatter. Long-form essays may produce longer notes. If a single source produces over ~600 lines, something is wrong — either the source is doing too many things and should be split into multiple notes, or extraction has bloated. Surface this and ask.

## Behaviors

### 1. Separate factual from promotional, every time

The Key Claims section is for what the post *asserts*. The Marketing / Promotional Language section is for what the post is *selling*. Even sober-looking posts have promotional elements (a researcher promoting their framework, a trader promoting their newsletter). The split is non-negotiable.

If a post has zero promotional content, write "None identified" under that heading. Do not skip the heading.

### 2. Quote sparingly, paraphrase by default

Under 15 words per quote, one quote per source maximum where it actually matters. Paraphrase everything else. The note is a *distillation*, not a reproduction.

### 3. Confidence is about the source, not the extraction

`confidence: low` does not mean "I'm not sure I extracted this right." It means "the source itself is weak."

**Before assigning confidence, consult `../references/quality-signals.md`.** That file contains the positive-signal diagnostics (primary-source posture, benchmark grounding, llms.txt presence, reproducible claims) that distinguish `high` from `medium` from `low`. Do not assign `high` without at least one positive signal from that checklist. Do not assign `low` without naming at least one specific weakness in `confidence_note`.

### 4. Testable is a binary commitment

`testable: true` means there is at least one claim in the post that *you, the user, could actually go test* — with code, data, or a small experiment. If the post is pure opinion, `testable: false`. Don't hedge. This field exists to make the vault queryable later: "show me everything I haven't validated yet."

### 5. My Flags is where the skepticism lives

This is the section that earns its keep. **Before filling this section, consult `../references/skepticism-heuristics.md`** for the cross-domain flag catalog (author-already-seen check, code-without-results check, academic-affiliation diagnostic, and the general flags that apply regardless of domain).

If the post is in the trading or quant domain, also consult `../references/trading-flags.md` for domain-specific patterns (template markers from known content factories, backtest-omission patterns, win-rate without dataset disclosure).

If nothing flags, write "Nothing flagged on first pass" — do not invent flags to fill the section.

### 6. URL fetch failure → fail loud

If `web_fetch` returns nothing usable, paywalled content, JS-rendered shell, or otherwise insufficient material, state this explicitly:

> Could not extract usable content from {URL}. Reason: {reason}. Options: (1) paste the raw text, (2) proceed with partial content, (3) skip.

Do not invent content. Do not fall back to summarizing from training data without saying so.

### 7. If author or date are unknown, say so

Empty fields get the value `unknown`, never blank or fabricated. Better to have `author: unknown` than to guess.

### 8. Filename slug discipline

Format: `YYYY-MM-DD-short-slug.md`. Slug is 3-6 words, kebab-case, lowercase, drawn from the title or core claim. Date is the capture date, not the publication date — the inbox sorts by when *you* saw it.

Examples:
- `2026-05-24-anthropic-skills-launch-claims.md`
- `2026-05-24-funding-rate-harvest-risks.md`
- `2026-05-24-laundromat-acquisition-seller-financing.md`

### 9. Verify named product features before treating them as real

When a post asserts specific Claude/Anthropic features, slash commands, or product capabilities exist: web-search → fetch primary source → confirm. Posts claiming personal-engineer slash commands are built-in features are common slop (Archetype F). Do not stub on content-cadence signals alone — verify first. See `../references/archetypes.md` § Verification-before-stubbing for the full rationalization-failure table.

### 10. Named tools/skills/repos without links → web-search for upstream

Web-search one named-but-unlinked tool or repo to verify it exists and matches the description. If descriptions match a longer-form article or GitHub repo, that's the real source — reclassify as pointer-to-primary or derivative.

### 11. GitHub-repo pre-check before full fetch

Before fetching a GitHub repo, check star count, commit count, and language breakdown. ≤10 commits + ≤5 stars + single-language shell = likely install funnel; stub. High commits + diverse footprint + active recent commits = extract. See `../references/skepticism-heuristics.md` for the full pre-check criteria.

### 12. Same-author template short-circuit

After the 3rd+ post by the same author, use `extraction_mode: abbreviated` with an `overlap_with:` frontmatter field. Capture only genuinely new material. Applies to same-template-different-author posts too — the template is the repeat, not the person. See `../references/trading-flags.md` for known template markers.

### 13. X posts pitching a workflow or product → wrapper-and-content check

Strip the marketing frame, ask: what is the actual technical claim? Novel and verifiable → extract, strip wrapper in TL;DR. Previously captured → skip, log as "wrapper-and-content, [[prior-note]]". Pure wrapper → stub as content-factory-template. See `../references/archetypes.md` Archetype G for the full decision tree.

### 14. Banned openers and phrases

Do not begin any section with these openers — they signal LLM-default filler, not distillation:

- "In conclusion..."
- "This post explores..."
- "The author argues..."
- "Overall, this is a..."
- "It is worth noting that..."
- "Notably,..."
- "Importantly,..."
- "Interestingly,..."
- "In summary,..."
- "To summarize,..."

Banned anywhere in note body:
- "game-changer" / "game changing"
- "revolutionary" / "revolutionary approach"
- "powerful" as a standalone adjective with no specifics
- "leveraging" (use "using")
- "seamlessly" (almost always content-factory filler)
- "cutting-edge" / "state-of-the-art" (use version numbers or benchmark citations instead)

If you catch yourself writing any of these, rewrite the sentence from scratch.

### 15. Rewrite-trigger self-check before finalizing output

Before producing the final note, run this self-check against the draft:

- Does any Key Claim bullet start with "The post says" or "The author claims"? → Strip the attribution, state the claim directly.
- Does the TL;DR editorialize? → Move opinions to My Flags.
- Does My Flags contain anything that isn't a concrete, specific concern? → Rewrite to name the specific failure mode.
- Is any bullet in Key Claims longer than 2 lines? → Split or compress.
- Does the note reproduce the structure of the source document section-by-section? → Reorganize by claim type, not source structure.
- Are there more than 3 bullets under Marketing/Promotional Language that say essentially the same thing? → Consolidate to one representative entry.

If any check fails, fix before outputting.

### 16. Batch, video, and PDF input modes

For full procedures on batch inputs (multiple URLs), YouTube/video URLs, and PDF/uploaded documents, see `../references/input-modes.md`. Summary:

- **Batch:** list the queue, process sequentially, checkpoint after each note, stop on ambiguity.
- **Video:** attempt `web_fetch`; if JS shell only, fail loud and ask for transcript paste. Never summarize from training data.
- **PDF:** read from `/mnt/user-data/uploads/`; surface document type and page count first; use `source_type: paper` for academic PDFs; fail loud on image-only scans; extract key claims not every section.

## Reference files

This skill loads these reference files only when relevant, per progressive disclosure:

- **`../references/skepticism-heuristics.md`** — cross-domain flag catalog. Consult when filling "My Flags." Applies to every extraction regardless of domain.
- **`../references/quality-signals.md`** — positive credibility diagnostics. Consult when assigning the `confidence` frontmatter field. Applies to every extraction.
- **`../references/trading-flags.md`** — domain-specific patterns for trading/quant content (content-factory templates, backtest omission). Consult only when the source is in the trading or quant domain.
- **`../references/archetypes.md`** — catalog of identified post archetypes (fabricated-numbers, real-product-fabricated-specifics, tool-promotion, accurate-but-derivative, pointer-post, fabricated-feature listicle). Consult during triage classification. Applies to every extraction.
- **`../references/input-modes.md`** — full procedures for batch inputs, YouTube/video URLs, and PDF/uploaded documents. Consult when handling any non-URL input mode.
