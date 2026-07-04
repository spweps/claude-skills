# Archetypes — Post Extractor Reference

## When to load this file

Load during triage classification. This file catalogs identified post archetypes — recurring patterns of source types, each with a classification, confidence default, and the specific signals that distinguish it. Use it to confirm or challenge your initial classification before producing output.

---

## Table of Contents

1. Archetype A: Primary Source
2. Archetype B: Pointer-to-Primary
3. Archetype C: Accurate-but-Derivative
4. Archetype D: Content-Factory Template
5. Archetype E: Real-Product, Fabricated-Specifics
6. Archetype F: Fabricated-Feature Listicle
7. Archetype G: Tool-Promotion / Affiliate Funnel
8. Archetype H: Wrapper-and-Content
9. Verification-before-stubbing rule
10. Edge cases and escalation

---

## Archetype A: Primary Source

**Classification:** `primary source` → full extraction  
**Confidence default:** `medium` to `high` depending on quality signals

**Defining characteristic:** The post contains information that could not exist without the author having done the underlying work. Original data, original experiment, original trade, original build.

**Positive signals:**
- Author is reporting their own results, not summarizing others'
- Specific non-round numbers that trace to real measurements
- Methodology described specifically enough to replicate
- Limitations acknowledged unprompted
- On-chain, API, or other verifiable data source provided

**Watch for:** Primary source framing with derivative content underneath. An author can claim "I analyzed 1,000 bots" (primary-source language) while actually doing a surface taxonomy with no disclosed methodology (closer to Archetype C). The test: could this post exist without the author having done the work? If yes, it's not a primary source.

---

## Archetype B: Pointer-to-Primary

**Classification:** `pointer-to-primary` → fetch and extract the target; stub the pointer  
**Confidence default:** depends on the target, not the pointer

**Defining characteristic:** The post's primary function is to surface or link to another source. X posts linking to GitHub repos, papers, or articles. Newsletter issues summarizing a study. Thread recommending a tool.

**Signals:**
- Post contains a link to a more substantive external source
- The post's value is discovery, not content
- Removing the link leaves nothing worth extracting

**Action:** Fetch the target. Extract the target as the primary note. Create a one-paragraph stub for the pointer noting that it led to the target. If the target is already in vault, skip both.

**Watch for:** Posts that are both pointer AND add original commentary. If the commentary is substantial and novel (not just "this is interesting"), extract the commentary as a separate thin note in addition to the target.

---

## Archetype C: Accurate-but-Derivative

**Classification:** `accurate-but-derivative` → stub  
**Confidence default:** `low` to `medium`

**Defining characteristic:** The post contains real, accurate information — but the information comes entirely from better primary sources that exist elsewhere. The post adds no original data, experiment, or synthesis. It is a guided tour of existing knowledge.

**Signals:**
- Post includes a reading list or bibliography that is the real value
- Claims are accurate but traceable to a named primary source
- Author's contribution is curation or summarization, not analysis
- Post could have been written by anyone who read the same sources

**Action:** Stub only. In the stub's "What's actually true" section, name the primary sources the post draws from. In "What to read instead," link those primary sources if they're in vault or flag them for extraction if they're not.

**Common examples:**
- X threads summarizing a paper without citing it
- "X lessons from Y book" posts that accurately paraphrase the book
- Newsletter roundups of research the author didn't conduct
- Any post whose bibliography is more valuable than its body (Principle 1 from `skill-authoring-lessons.md`)

---

## Archetype D: Content-Factory Template

**Classification:** `content-factory template` → stub  
**Confidence default:** `low`

**Defining characteristic:** Post is generated from a repeatable template. Format, hook, structure, and CTA are identical across multiple posts — possibly from the same author, possibly from a network of authors using the same playbook.

**Signals (three or more → classify as content-factory):**
- Identical opening structure to known prior posts
- Engagement-bait hook (income claim, "bookmark this," urgency frame)
- Numbered list of N things with no connective reasoning between items
- No original data, experiment, or primary-source claim anywhere
- Closing CTA that is the post's actual purpose (follow, course, community)
- Could have been written by an LLM given a brief

**Action:** Stub. One-paragraph TL;DR, My Flags noting the template markers observed. No Key Claims section — there are no claims worth extracting.

**Watch for:** Content-factory format wrapping genuine primary-source content. A practitioner who uses a formulaic thread format may still be sharing real experience. Classify by the content, not the format. See Archetype H (Wrapper-and-Content) for this case.

---

## Archetype E: Real-Product, Fabricated-Specifics

**Classification:** `real-product, fabricated-specifics` → stub  
**Confidence default:** `low`

**Defining characteristic:** A real product, tool, or platform is the subject. The post makes specific claims about the product (features, version numbers, performance metrics, dates) that are invented, outdated, or incorrect — while the product itself is legitimate.

**Signals:**
- Product is real and verifiable
- Named features don't exist or work differently than described
- Version numbers, dates, or metrics don't match current documentation
- Install commands or slash commands are fabricated
- Internal arithmetic on stated metrics fails

**Most common form:** AI tool posts claiming specific Claude/Anthropic slash commands or skill behaviors that don't exist as described. The tool (Claude) is real; the claimed features are invented.

**Action:** Stub. In My Flags, name the specific fabricated elements and what the real behavior is. In "What's actually true," describe what the product actually does. In "What to read instead," link to the product's actual documentation.

**Verification step (§9 of SKILL.md):** Before stubbing as this archetype, verify at least one named feature against the primary source (official docs, GitHub, llms.txt). Promotional surface alone is not sufficient to classify as fabricated-specifics — the fabrication must be confirmed.

---

## Archetype F: Fabricated-Feature Listicle

**Classification:** `fabricated-feature listicle` → stub  
**Confidence default:** `low`

**Defining characteristic:** Post format mimics product documentation, feature lists, or changelogs. Items are presented in the visual grammar of `/help` output, a product changelog, or an official feature list. A significant fraction of the items are invented, misattributed, or do not exist as described.

**Signals:**
- Format: numbered or bulleted list of "features," "commands," or "capabilities"
- Visual grammar matches product documentation
- Items reference specific slash commands, model behaviors, or API endpoints
- One or more items fail verification against actual product docs
- Post claims to be comprehensive ("all," "complete," "every")

**Distinction from Archetype E:** Archetype E wraps a real product in fabricated metrics (dates, star counts, performance numbers). Archetype F wraps a real product in a fabricated feature list. The format is the tell — if it looks like a changelog or command reference, it's Archetype F.

**Action:** Stub. Verify one or two specific items against primary source documentation before stubbing. Name the specific items that failed verification in My Flags.

---

## Archetype G: Tool-Promotion / Affiliate Funnel

**Classification:** `tool-promotion` → stub  
**Confidence default:** `low`

**Defining characteristic:** The post uses a real subject (a problem, a concept, a use case) as a credibility hook to drive readers toward a specific tool, product, or service — usually via an affiliate link, referral code, or direct CTA. The subject is accurate; the purpose is conversion.

**Signals:**
- Post introduces a real problem or concept accurately in the first third
- Middle section pivots to a specific tool as the solution
- Closing CTA drives to the tool (affiliate link, referral code, signup, "DM me")
- The same content could describe multiple tools, but only one is named
- Author benefit from the conversion is evident (affiliate tag in URL, disclosed or not)

**Distinction from legitimate tool posts:** A practitioner honestly recommending a tool they use is not Archetype G. The tell is the funnel structure: problem → specific tool → conversion CTA. Legitimate recommendations don't have the third step as the structural destination.

**Action:** Stub. In My Flags, name the tool being promoted and note the funnel structure. In "What's actually true," describe the underlying concept or problem honestly if it has vault value. In "What to read instead," link to primary sources on the concept that aren't affiliated with the tool being promoted.

---

## Archetype H: Wrapper-and-Content

**Classification:** varies → see §13 of SKILL.md  
**Confidence default:** depends on the core content

**Defining characteristic:** A promotional or engagement-bait wrapper (income claim, urgency hook, follow CTA) surrounding genuine substantive content. The wrapper is marketing; the core is real.

**This is not a classification — it is a diagnosis that precedes classification.** After identifying the wrapper, strip it and classify the core:

- Core is novel and verifiable → `primary source`, full extraction, strip wrapper in TL;DR
- Core has been captured before → skip, log as "wrapper-and-content, [[prior note]]"
- Core is accurate-but-derivative → `accurate-but-derivative`, stub
- Post is pure wrapper with no substantive core → `content-factory template`, stub

**Signals of a wrapper:**
- Income claim as the hook ($X/month, "I made $Y doing Z")
- "Bookmark this" / "Save this" urgency framing
- "Follow for more" CTA
- Engagement bait ("Most people won't do this")
- Third-person framing about someone else's success ("This person made $X")

**Signals of substantive core:**
- Specific technical mechanism described
- Named wallet address, account, or verifiable data source
- Process described specifically enough to attempt
- Non-round numbers traceable to real measurements

**The key rule:** Do not let the wrapper determine the classification. Classify by what's inside.

---

## 9. Verification-before-stubbing rule

Content-cadence packaging (Telegram CTA, "follow me" framing, numbered listicle format) correlates with fabrication but is NOT identical to it. A post can have promotional surface AND quote primary sources verbatim.

**Before stubbing any post as Archetype D, E, F, or G:**
1. Identify the strongest specific claim in the post
2. Verify it against one primary source (official docs, GitHub, on-chain data, web search)
3. If the claim holds up: reconsider the classification. The format may be promotional while the content is real.
4. If the claim fails: stub with confidence. Name the failed claim in My Flags.

**Rationalization-failure table:**

| Surface signal | Tempting rationalization | Correct behavior |
|---|---|---|
| "Follow me" CTA | "It's just self-promotion, the content might be real" | Verify one specific claim before deciding |
| Telegram group link | "Community can still have real signal" | Affiliate/referral structure → flag regardless; verify content independently |
| Numbered listicle format | "Lists can be real" | Verify at least one item against primary source |
| Income claim headline | "Could be real, people do make money" | Unverifiable without on-chain source → flag; verify content separately |
| AI-generated prose style | "LLMs can write accurately" | Higher bar for verification; LLM-generated specificity is a known failure mode |

The goal is to be neither too trusting nor too dismissive. The skepticism filter exists to protect the vault from noise, not to exclude real signal.

---

## 10. Edge cases and escalation

**Ambiguous classification:** If after triage you genuinely cannot determine the classification, write `classification: ambiguous` and ask the user with one specific question: "Is this [X] or [Y]? The distinction matters because [reason]." Do not produce a note until the classification is resolved.

**Multiple archetypes in one post:** A post can be simultaneously Archetype B (pointing to a primary source) and Archetype G (with an affiliate link). Handle both: stub the post for the affiliate structure, extract the target for the primary source content.

**Novel archetype:** If a post doesn't match any archetype in this catalog but clearly warrants classification before extraction, classify it as the closest match and flag it in My Flags as a potential new archetype. Sufficient evidence (3+ similar instances) → promote to `skill-authoring-lessons.md` and add here.
