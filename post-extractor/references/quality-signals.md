# Quality Signals — Post Extractor Reference

## When to load this file

Load when assigning the `confidence` frontmatter field. This file defines the positive-signal diagnostics that distinguish `high` from `medium` from `low`. The rules:

- Do not assign `confidence: high` without at least one positive signal from this file
- Do not assign `confidence: low` without naming at least one specific weakness in `confidence_note`
- `confidence: medium` is the honest default when signals are mixed or absent

Confidence is about the **source's credibility**, not extraction accuracy.

---

## Table of Contents

1. Positive signals → toward `high`
2. Negative signals → toward `low`
3. Calibration rules
4. confidence_note templates

---

## 1. Positive signals (toward `high`)

Each signal below is evidence of credibility. No single signal is sufficient alone — look for clusters of two or more before assigning `high`.

**Primary-source posture**
The author is reporting original work: their own data, their own code run on their own dataset, their own experiment, their own deal. Not summarizing, not paraphrasing, not synthesizing others' work. The post could not exist without the author having done the thing.

**Benchmark grounding**
Claims are compared against a named baseline: buy-and-hold, an index, a prior method, a published benchmark dataset. "Our approach achieves X" is weak. "Our approach achieves X vs. Y baseline on Z dataset" is grounded.

**Reproducible claims**
Results can be reproduced by a reader with the stated inputs. Code is provided AND the code's own output is reported. Dataset is named and accessible. Methodology is specific enough to replicate.

**Exposed rigor artifacts**
The repo or post exposes: a `benchmark/` directory, a `tests/` directory with passing tests, an arxiv or peer-reviewed paper link, a reproducible evaluation script. These are infrastructure investments that correlate with serious work.

**Specific non-round numbers**
Real measurements produce irregular figures: 67.3% win rate, Sharpe of 1.84, $143,219 P&L. Round numbers (70%, 2.0 Sharpe, $100K) suggest fabrication or heavy rounding.

**Verifiable on-chain or external data**
Claims are backed by a public data source a reader can check: a wallet address, a transaction hash, an API endpoint, a public dataset. For Polymarket: proxyWallet address provided and activity matches claims.

**`llms.txt` at a stable URL** *(decay-bound signal — see note below)*
Project publishes a structured `llms.txt` at its documentation root. Signals investment in machine-readable infrastructure; currently correlates with projects run by people who think carefully about how AI tools consume their work. **Decay note:** this signal will weaken as adoption grows (estimated 12-24 months from June 2026). When it becomes routine, its absence will be the remaining signal. Check `skill-authoring-lessons.md` Principle 10 for the updated guidance when that happens.

**Acknowledged limitations**
The author explicitly names what their work does not cover: out-of-sample periods, fee sensitivity, asset-specific risks, sample size constraints. Unprompted acknowledgment of limitations is a strong credibility signal — it means the author ran into the limits and is being honest about them.

**Track record with verifiable history**
Author has a documented history of prior work that checks out: prior posts with reproducible results, a GitHub commit history showing real iterative development, a published paper with citations.

---

## 2. Negative signals (toward `low`)

Each signal below is evidence against credibility. Two or more → `confidence: low`.

**Fabricated specifics**
Internal arithmetic fails: claimed percentages don't match the numbers given, claimed totals don't add up, claimed dates conflict with other stated dates. This is the fastest single check — run the numbers before anything else.

**Methodology withheld on a quantitative claim**
"I analyzed 1,000 X and found Y" with no description of how X were selected, what "profitable" means, what timeframe was studied, or how Y was measured. The headline number requires the methodology to be meaningful.

**Unverifiable income claim as the hook**
"$40,000/month," "$143K profit," "I made X doing Y" with no on-chain source, no account link, no verifiable data. Income claims are the primary hook in content-factory posts.

**Referral or affiliate structure**
URL parameters (`?via=`, `?ref=`, `?affiliate=`), course upsell at the close, "DM me for the full strategy," "join my community." The monetization structure is the post's purpose.

**LLM-generated specificity**
Specific-sounding details (tool names, version numbers, percentages, dates) that don't survive a web search. This is the "real-product, fabricated-specifics" archetype — the product is real, the claimed features or numbers are invented.

**Star count / social proof inconsistency**
GitHub stars, follower counts, or download numbers cited in the post don't match current reality by more than 20%. Posts often freeze these numbers at time of writing; large discrepancies suggest the post is significantly outdated or the numbers were inflated.

**Code not run**
Implementation is provided but the post never reports what the code produces. See `trading-flags.md` §3 for the full treatment of this pattern.

**Academic branding without rigor artifacts**
University/lab prefix with no paper, no benchmark directory, no reproducible metrics. See `trading-flags.md` §7.

**Same-template-different-author**
Post is structurally identical to a prior extraction by a different author. The template is the content factory; the individual author is interchangeable.

---

## 3. Calibration rules

**`confidence: high`** — Two or more positive signals, zero strong negative signals. Source is doing original work and showing their work.

**`confidence: medium`** — Mixed signals, or positive signals present but not clustered. Typical for: real products described accurately but without benchmarks; practitioner experience posts without reproducible data; taxonomies or frameworks without empirical validation. This is the honest default.

**`confidence: low`** — One or more strong negative signals (fabricated specifics, unverifiable income claim, methodology withheld, referral structure). Or: zero positive signals on a post making quantitative claims.

**Never assign `high` to:**
- Any post where internal arithmetic fails
- Any post whose primary claim is an unverified income figure
- Any post where the methodology for a quantitative claim is withheld

**Never assign `low` without naming the specific weakness** in `confidence_note`. "Source quality unclear" is not a confidence note. "Win rate claim has no dataset, timeframe, or sample size" is.

---

## 4. confidence_note templates

Use these as starting points. Always customize to the specific source.

**For `high`:**
> "Primary-source posture: author reports own [data/experiment/trade history] with reproducible methodology and [specific positive signal]."

**For `medium` (mixed signals):**
> "Real [product/practitioner/analysis] with accurate core claims, but [missing element — benchmark, methodology, out-of-sample]. Cannot assign high without [what would be needed]."

**For `medium` (no signals either way):**
> "Unable to assess — [author unknown / no verifiable data / no external reference]. Defaulting to medium."

**For `low` (fabricated specifics):**
> "Internal arithmetic fails: [specific example of inconsistency]. Fabricated-specifics classification."

**For `low` (methodology withheld):**
> "Quantitative claim ('[claim]') made without disclosed methodology — sample, timeframe, and selection criteria all absent."

**For `low` (referral/affiliate):**
> "Referral structure present ([specific evidence]). Post's purpose is traffic generation, not information sharing."

**For `low` (academic branding without rigor):**
> "Academic branding ([affiliation]) without paper link, benchmark directory, or reproducible metrics. Branding is marketing."
