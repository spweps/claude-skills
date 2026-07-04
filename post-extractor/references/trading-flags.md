# Trading Flags — Post Extractor Reference

## When to load this file

Load when the source being extracted is in the trading or quant domain: prediction markets, crypto, equities, options, algorithmic trading, quant research, backtesting, trading bots, market microstructure, or anything making performance claims about a trading strategy.

Do not load for general finance, business acquisition, or macro commentary — those are covered by `skepticism-heuristics.md`.

---

## Table of Contents

1. Template markers from known content factories
2. Backtest omission patterns
3. Code-without-results flag
4. Performance claim red flags
5. Author-already-seen check
6. Polymarket-specific flags
7. Academic-branding-without-rigor flag
8. Flag language guide

---

## 1. Template markers from known content factories

The following structural patterns indicate a content-factory post rather than original analysis. When three or more markers are present, classify as `content-factory template` regardless of the technical content.

**Quant career / trading roadmap template markers:**
- Opening line introduces the author by name and credential ("I'm [name], a [role] at...")
- "Bookmark this" in the opening
- "If you are looking for a shortcut, this is not for you" mid-post
- Identical closing question phrasing across posts ("There is no wrong answer but there are very revealing ones")
- Named hedge-fund credibility framing (Citadel, Two Sigma, Renaissance, Jane Street) without direct affiliation claim
- Five to six numbered parts with math/code body
- Caveats acknowledged late, after the technical content
- No reported personal numbers anywhere in the post
- Cross-promotion of the same author's other posts at the close

**General quant content-factory markers:**
- "I analyzed N [trades/bots/strategies]" with no methodology disclosure
- Win rate claim without dataset, timeframe, or sample size
- Strategy described in full with no mention of fees, slippage, or execution costs
- "This strategy returned X%" with no benchmark comparison
- Complete runnable code with no reported results from running it
- Reading list as the core value (the bibliography IS the post)

When same-author template markers are present across multiple posts in a session, apply the same-author short-circuit (§12 of SKILL.md) even if the topics differ. One author repeating a template is one source, not multiple.

---

## 2. Backtest omission patterns

Backtests are the primary manipulation surface in trading content. Flag any of the following:

- **No timeframe:** "This strategy works" with no period specified
- **No benchmark:** Returns reported without comparison to buy-and-hold or relevant index
- **No drawdown:** Win rate or return reported without maximum drawdown
- **No out-of-sample:** In-sample backtest only; no walk-forward or held-out test period
- **Survivorship bias unaddressed:** Strategy tested on assets that exist today, not the full historical universe
- **Look-ahead bias unaddressed:** Strategy uses data that would not have been available at trade time
- **Fees omitted:** Gross returns reported, net returns not calculated
- **Slippage omitted:** Especially critical for high-frequency or small-cap strategies

Flag language: "Post reports [metric] without [missing element] — backtest result is uninterpretable without it."

When a post provides full backtest code but omits the code's own reported results, flag explicitly:
> "Author provides full backtest implementation but omits the backtest's own reported output numbers. Diagnostic of unimpressive results, incomplete work, or overselling."

---

## 3. Code-without-results flag

This is the loudest single signal of overselling in trading/quant content. Applies when:

- Post includes complete, runnable Python/R/Julia implementation
- Post never reports what the code produces on the recommended dataset
- The omission is structural (appears throughout, not a single missing table)

The three possible explanations in order of likelihood:
1. Work was done, results were unimpressive, omitted deliberately
2. Work was done partially, results were inconsistent, omitted
3. Code is stitched from textbook/Stack Overflow material, never actually run

Explanation 1 is most likely. Explanation 3 is increasingly common in LLM-assisted content.

Flag language: "Full [strategy name] implementation provided ([class/function names]) with no reported output on the stated dataset ([dataset]). Code-without-results is the primary overselling signal in trading content."

---

## 4. Performance claim red flags

**Round numbers:** Win rates of exactly 70%, 75%, 80% are suspicious. Real backtests produce 67.3%, 71.8%. Round numbers suggest fabrication or rounding to hide variance.

**Suspiciously high Sharpe:** Any Sharpe above 2.0 on a daily or longer strategy requires extraordinary evidence. Above 3.0 is almost certainly data-mined or look-ahead contaminated.

**Short timeframe:** A strategy "working" over 3-6 months proves nothing. Minimum credible backtest for a mean-reversion strategy is 3-5 years across multiple regimes.

**Single asset:** A strategy validated only on BTC or SPY is not validated — it may be fitting to that asset's specific regime.

**No losing periods:** Any strategy claiming no drawdown periods is either cherry-picked, look-ahead contaminated, or fabricated.

**Prediction market specific:**
- "$X profit" claims without specifying number of trades, timeframe, or whether fees are included
- Win rate without trade count (1 trade with 100% win rate is meaningless)
- P&L from a single screenshot without on-chain verification
- Claims about "always profitable" outcomes in markets with dynamic fee structures

---

## 5. Author-already-seen check

Before extracting any trading post, check whether the author has been extracted before in this session or in the vault.

If yes:
1. Check for template markers (§1). If present across posts, this is serial content-factory output.
2. Weaken any cross-source convergence claim: two posts by the same author agreeing on a strategy are one data point, not two.
3. Apply same-author short-circuit (§12): capture only genuinely new material.
4. Flag in My Flags: "Author previously extracted ([prior note]). Template markers [present/absent]. Treat convergence with prior posts as serial output, not independent confirmation."

Same-author network check: if the author and a prior-extracted author cross-promote each other, they are the same source for confirmation purposes.

---

## 6. Polymarket-specific flags

**On-chain verification standard:** Any Polymarket performance claim (profit, win rate, volume) is verifiable via `data-api.polymarket.com/activity?user={proxyWallet}&limit=500`. If the post does not provide a wallet address and the claim is material, flag as unverifiable.

**Referral links:** Polymarket URLs containing `?via=XXX` are referral links. The poster earns a referral fee if the reader signs up. Flag as tool-promotion regardless of other content quality.

**Bot classification taxonomy:** As of June 2026, established bot taxonomies identify six primary bot types on prediction market Up/Down markets: Pure Arbitrage, Directional Arbitrage, Repricing/Fair Value, Cross-Timeframe, Imbalance, Near-Resolution. When a post describes a prediction market bot strategy, classify it against this taxonomy and note which type.

**Fee regime awareness:** Polymarket introduced dynamic taker fees in January 2026. Any strategy claiming edge on Up/Down markets without addressing the fee structure post-January 2026 is incomplete. Pre-2026 backtest results do not transfer.

**Regime boundaries for analysis:**
- March 30, 2026: dynamic taker fee rollout
- April 28, 2026: V2 cutover
- May 29, 2026: Wintermute/institutional MM entry
When a post makes claims about Up/Down market performance, note which regime the claimed results fall in.

---

## 7. Academic-branding-without-rigor flag

Applies when a trading or quant post/repo carries academic-style branding (university prefix, "lab," "research group," named institution).

Run the three-diagnostic check:
1. Does the work link to an actual paper (arxiv, peer-reviewed venue, technical report)?
2. Does the repo contain a `benchmark/` directory with reproducible numbers tied to a named dataset?
3. Does the work report specific metrics (Sharpe, accuracy, R@K) against a stated baseline?

If all three fail: flag as academic-branding-without-rigor.

Flag language: "Source carries academic-style branding ([affiliation]) but no paper link, no benchmark directory, and no quantitative performance reporting against a baseline. Academic posture is marketing, not evidence."

The inverse: work with rigor artifacts (benchmark dir, paper link, reproducible numbers) but no academic affiliation still earns the confidence credit. Rigor is the signal; branding is the wrapper.

---

## 8. Flag language guide

Use these exact phrasings in My Flags for consistency and future Dataview queryability:

| Pattern | Flag text |
|---|---|
| Code without results | "Full implementation provided, backtest results omitted — primary overselling signal." |
| Backtest missing element | "Backtest omits [timeframe / drawdown / out-of-sample / fees / benchmark] — result uninterpretable." |
| Round-number win rate | "Win rate of [X]% is suspiciously round — real backtests produce irregular figures." |
| Author already seen | "Author previously extracted ([[note]]). Treat convergence as serial output, not independent confirmation." |
| Referral link | "URL contains referral tag (?via=XXX) — poster earns referral fee. Tool-promotion classification." |
| Unverifiable P&L | "[$X] profit claim unverifiable — no wallet address provided, no on-chain source." |
| Academic branding without rigor | "Academic branding ([affiliation]) without paper, benchmark directory, or reproducible metrics." |
| Pre-fee-change backtest | "Results pre-date January 2026 dynamic taker fee rollout — do not transfer to current regime." |
