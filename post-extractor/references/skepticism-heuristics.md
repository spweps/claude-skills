# Skepticism Heuristics — Post Extractor Reference

## When to load this file

Load when filling the "My Flags" section of any extraction, regardless of domain. This is the cross-domain flag catalog — it applies to every source. For trading/quant content, also load `trading-flags.md` after this one.

---

## Table of Contents

1. Author-already-seen check
2. Code-without-results check (general)
3. Fabricated-specifics check
4. Source-topology check
5. Promotional-surface check
6. General flags (apply to any domain)
7. What to do when nothing flags
8. Flag writing standards

---

## 1. Author-already-seen check

**Run this before extraction, not after.**

Search the vault for prior notes from this author. If found:
- Check for template markers (same hook, same structure, same CTA)
- If template markers match: this is serial content-factory output. Apply same-author short-circuit (§12 of SKILL.md)
- If template markers don't match: author is a repeat source. Flag it, but proceed with full extraction if the content is new
- Weaken any cross-source convergence claim: same author agreeing with themselves across posts is one data point, not two

**Same-author network check:** If the author of this post cross-promotes a previously-extracted author, treat them as the same source for confirmation purposes. A promoter→creator→co-promoter loop is one node, not three.

Flag language: "Author previously extracted ([[prior note]]). Template markers [match/don't match]. [Apply short-circuit / proceed with awareness of serial-output pattern]."

---

## 2. Code-without-results check (general)

When a post includes runnable code as evidence for a claim:

1. Does the post report what the code actually produces?
2. Is the reported output consistent with the code's logic?
3. Is the dataset the code runs on named and accessible?

If code is present and results are absent: flag. This pattern appears across domains — trading strategies, ML models, data pipelines, scraping tools. The explanation is almost always: results were unimpressive, work was incomplete, or code is illustrative rather than functional.

See `trading-flags.md` §3 for the domain-specific treatment in quant/trading content.

---

## 3. Fabricated-specifics check

Run internal arithmetic on any specific numbers before writing Key Claims.

**Check:**
- Do claimed percentages match the underlying figures?
- Do claimed totals add up from stated components?
- Do claimed dates/timelines cohere with each other?
- Do named version numbers, star counts, or metrics match current external reality?

**If arithmetic fails:** This is a `confidence: low` signal and a My Flags entry regardless of how credible the rest of the post seems. Flag the specific failure, not a general suspicion.

Flag language: "Internal arithmetic fails: [specific example]. Posts with fabricated specifics often have fabricated everything — treat all quantitative claims with heightened skepticism."

**Star count / version check:** For posts citing GitHub stars, follower counts, or product version numbers, spot-check one figure. A 20%+ discrepancy from current reality suggests the post is significantly outdated or the number was inflated at time of writing.

---

## 4. Source-topology check

Before classifying, map the source's position in the information chain:

**Tier 1 (highest signal):** Author is the primary source. They did the work, ran the experiment, built the thing, made the trade. The post could not exist without them having done it.

**Tier 2 (medium signal):** Author is a practitioner synthesizing their own experience with external sources. The synthesis adds value even if the underlying sources exist elsewhere.

**Tier 3 (low signal):** Author is summarizing, paraphrasing, or repackaging Tier 1 or Tier 2 content. The post's value is discovery (surfacing a primary source) not content. Classify as accurate-but-derivative or pointer-to-primary accordingly.

**Tier 4 (noise):** Author is generating content-shaped text. The post mimics the form of Tier 1 or 2 content but contains no original information, experience, or synthesis. Classify as content-factory template.

Most AI/tech/trading content on X is Tier 3 or Tier 4. Ask: "What does this post know that no other post about this topic knows?" If the answer is nothing, it's Tier 3 or lower.

---

## 5. Promotional-surface check

Promotional surface is not the same as promotional content. A post can have promotional framing and still contain real primary-source information. Classify by what's inside, not by the wrapper.

**Promotional surface markers (adjust classification only if they dominate):**
- Income claim as the headline hook ("$40K/month," "I made $X")
- Engagement bait at the close ("Follow for more," "Save this," "RT if you agree")
- Urgency framing ("Don't miss this," "Limited time")
- Affiliate or referral links in the body
- Course, community, or coaching CTA
- "DM me for the full [strategy/template/guide]"

**The wrapper-and-content rule (§13):** Strip the wrapper mentally. What is the actual technical or operational claim? If the core claim is novel and verifiable → extract it, strip the wrapper in TL;DR. If the core claim has been captured before → skip. If the post is pure wrapper with no substantive core → stub as content-factory template.

**One marker is not a classification.** A post with a follow CTA at the end is not automatically a content-factory. A post whose entire value proposition is the follow CTA is.

---

## 6. General flags (apply to any domain)

These apply regardless of whether the domain is trading, AI, acquisition, infra, or other.

**Missing primary attribution**
Post makes specific claims attributed to a study, dataset, or analysis that is not linked or named specifically enough to find. "Research shows..." or "Studies have found..." without a citation is not evidence.

**Selective time window**
Performance, growth, or improvement claims that cover a suspiciously favorable period: bull market only, post-product-launch only, cherry-picked date range. Flag: "Results cover [period] — check whether the period was selected to maximize the apparent outcome."

**Anecdote as data**
One example (one trade, one acquisition, one user) presented as evidence of a general pattern. Valid as illustration; invalid as proof. Flag when the anecdote is doing the evidentiary work.

**Missing base rate**
"X% of people who do Y achieve Z" with no denominator. Success stories are always true; the base rate is the question. Flag: "Success rate claimed without denominator or base rate — uninterpretable."

**Definitional drift**
Key terms defined loosely or inconsistently within the post. "Profitable" meaning gross before fees in one section, net after fees in another. "Active users" meaning logins in one place, paying customers in another. Flag the specific inconsistency.

**Recency as credibility**
"As of [recent date]..." used to imply that the information is validated and current when it is actually just recently written. Recency of writing is not recency of validation.

**Social proof inflation**
Follower counts, testimonials, or "as used by X companies" claims that cannot be verified or that don't survive a search. Social proof is the easiest metric to fabricate.

**Security tool offensive-use framing**
Posts about OSINT, pentesting, or security tooling that describe only offensive use cases while the obvious defensive use case (opsec self-audit, exposure check) goes unmentioned. This is under-claim rather than over-claim — the tool's legitimate uses are being hidden, not inflated. Flag: "Tooling described exclusively in offensive framing; legitimate defensive use case ([specific use]) unmentioned."

---

## 7. What to do when nothing flags

If the post genuinely has no flags:

Write: "Nothing flagged on first pass."

Do not invent flags to fill the section. An unflagged extraction is a valid outcome and a high-signal note that the source passed the filter.

If you feel uncertain but can't name a specific concern: write "Uncertain — [describe the uncertainty specifically]. Not flagging without a concrete concern to name."

---

## 8. Flag writing standards

**Be specific, never general.** "Claims seem inflated" is not a flag. "Author claims 80% win rate on 47 trades with no dataset, timeframe, or fee disclosure" is a flag.

**Name the pattern.** When a flag matches a known archetype or pattern from this file or `trading-flags.md`, name it explicitly. This makes the vault searchable for that pattern later.

**One flag per concern.** Don't repeat the same concern with different wording to fill space. If the post has one problem, write one flag.

**Distinguish levels of concern:**
- Structural (affects classification): flag first, then classify accordingly
- Moderate (affects confidence): flag and note in `confidence_note`
- Minor (worth noting): flag with "minor" qualifier so future-you knows it's not blocking

**Flags are not verdicts.** A flagged post may still be valuable. The flag is information; the user decides what to do with it. Write flags that inform, not flags that dismiss.
