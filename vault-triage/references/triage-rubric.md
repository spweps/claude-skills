# Triage Rubric

Full decision criteria for the seven buckets used in vault triage. Load this file during Step 2 when a note's classification is ambiguous.

## Table of Contents

- [PROMOTE → 10_patterns/](#promote--10_patterns)
- [PROMOTE → 20_domains/](#promote--20_domainsdomain)
- [PROMOTE → 50_{project}/](#promote--50_project)
- [CROSS-LINK ONLY](#cross-link-only)
- [STUB](#stub)
- [DELETE](#delete)
- [HOLD](#hold)
- [Edge cases](#edge-cases)
- [Quick classification guide](#quick-classification-guide)

---

## PROMOTE → 10_patterns/

**Promote when ALL of:**
- The note synthesizes an observation that applies across multiple projects or domains (not just one bot, one market, one deal)
- The observation is derived from real evidence in the note, not asserted
- Three or more inbox notes point to the same observation (the three-source threshold from skill-authoring-lessons.md Principle 7) — OR — the note contains a complete original framework not derivative of a known source

**Do not promote when:**
- The note is a single data point (one backtest result, one tool review)
- The note is a stub
- The observation is domain-specific and has a clear home in `20_domains/`

**Worked example — promote:**
Three independent inbox notes all point to the same principle: validate strategies with honest trial counts before treating results as real. Three sources, cross-domain applicability → promote to patterns as a new "strategy-validation" note synthesizing the three.

**Worked example — don't promote:**
An acquisition-specific note from a single source about seller financing mechanics. Belongs in `20_domains/acquisition/` cross-link, not patterns.

---

## PROMOTE → 20_domains/{domain}/

**Promote when:**
- The note's `domain:` frontmatter matches an active project hub
- The note contains information a hub note should reference (tool, framework, repo, dataset, open question answer)
- Moving it would make it easier to find during active project work

**Destination mapping:**
- `domain: trading` + relevant to polybot → cross-link from `20_domains/trading/polybot.md`
- `domain: trading` + relevant to btcbot → cross-link from `20_domains/trading/btcbot.md`
- `domain: trading` + general → cross-link from `20_domains/trading/Trading.md`
- `domain: acquisition` → cross-link from appropriate `20_domains/acquisition/` hub
- `domain: infra` → cross-link from `20_domains/infra/` hub
- `domain: ai` + skill-authoring content → may extend `10_patterns/skill-authoring-lessons.md`

**Note:** "Promote to 20_domains/" in most cases means adding a `[[wiki-link]]` to the relevant hub note, not moving the file. The inbox note stays in `00_inbox/`; the hub note gains a backlink. Only move the file itself if it's a reference document that will be loaded directly during project work (e.g., a cheat sheet, a fee table, a dataset spec).

---

## PROMOTE → 50_{project}/

**Promote when:**
- The note documents a build session, decision log, or artifact that belongs to a standalone project
- The note is a postmortem (bug, dead-end, failed experiment with lessons) → `50_postmortems/`
- The note is company-brain consulting infrastructure → `50_company_brain/`

**Postmortem criteria:** A note is a postmortem if it documents:
- A specific bug with root cause identified
- A strategy or approach tried and abandoned with clear reason
- A tool evaluated and rejected with evidence
- A dead-end with enough detail that future-you won't repeat it

**Examples:**
- A postmortem documenting a dead trading strategy — e.g., a latency arb killed by fee structure changes. Clear dead-end, actionable lesson. → `50_postmortems/`
- A bug postmortem from a trading bot → `50_postmortems/`

---

## CROSS-LINK ONLY

**Use when:**
- The note stays in `00_inbox/` (appropriate home for it)
- But it answers an open question, provides supporting evidence, or is directly relevant to an active project hub note
- And that hub note doesn't yet have a `[[wiki-link]]` to it

**Action:** Add the `[[wiki-link]]` to the hub note's "Related notes" or "Open questions" section. Do not move the file.

**This is the most common triage action.** Most high-signal trading notes should be cross-linked to `polybot.md` or `btcbot.md` rather than promoted or deleted.

**Check these hub notes during triage:**
- `20_domains/trading/polybot.md` — high traffic; many inbox notes are polybot-relevant
- `20_domains/trading/btcbot.md`
- `20_domains/trading/Trading.md`
- `20_domains/trading/cross-venue-arb.md`
- `10_patterns/skill-authoring-lessons.md` — skill-authoring posts may extend this

---

## STUB

**Use when:**
- The note IS already a stub (content-factory, slop, derivative)
- No further action is needed
- The stub already has a "What to read instead" section if applicable

A stub assigned STUB in triage simply means: no action, correctly classified, leave it.

**Do not promote stubs.** A stub's value is its negative-space signal (this source is noise; here's what's real instead). That value lives in the inbox.

---

## DELETE

**Delete ONLY when:**

1. **Exact duplicate:** Same source URL appears in two notes with different filenames. Keep the more complete extraction; delete the other. State which to keep and why.

2. **Zero-value stub, source superseded:** The stub has no "What to read instead" section AND the underlying source has been fully superseded by a better extraction already in the vault.

**Do not delete:**
- Stubs with "What to read instead" — negative-space value
- Low-confidence notes — low confidence ≠ no value
- Notes that are boring but accurate
- Notes you haven't read carefully

When in doubt, HOLD or CROSS-LINK instead of DELETE.

---

## HOLD

**Use when:**
- The note has clear potential value but can't be acted on yet
- The revisit trigger is specific and named

**Mandatory:** Every HOLD entry must include a concrete revisit condition. "Needs more context" is not a condition.

**Common HOLD triggers for this vault:**
- Polymarket V2 calibration data (revisit when dry-run produces sufficient V2 trade history)
- GTA 6 launch confirmation (revisit when release date is public)
- btcbot regime data (revisit after next market regime shift)
- Acquisition target (revisit when acquisition thesis reaches execution phase)

---

## Edge cases

### Note already linked from a hub note

If the inbox note already appears as a `[[wiki-link]]` in a hub note → it's already been cross-linked. Triage action: verify the link is correct, then assign NO ACTION (or HOLD/PROMOTE if there's additional work to do beyond the existing link).

### Note with `extraction_mode: abbreviated`

These notes intentionally captured only new material relative to prior notes by the same author. They are correct as-is. Triage action: confirm the `overlap_with:` field names real notes in the vault. If the referenced notes exist → NO ACTION. If referenced notes are missing → flag as an orphaned abbreviated extraction.

### Note with open `- [ ]` items that have been resolved

If a note has open questions that have been answered by later inbox notes, patch the note to mark them resolved and add a cross-link. This is a CROSS-LINK action with a secondary note-patch.

### Cluster of notes from the same content-factory author

If 3+ notes from the same author are all stubs → note the cluster in the report under Structural Actions: "Author X has N stub notes; consider blocking further extractions from this source." Don't delete the stubs individually; surface the pattern.

---

## Quick classification guide

| Signal | Likely decision |
|---|---|
| `domain: trading` + `confidence: high` + not yet in polybot.md related | CROSS-LINK → polybot.md |
| `domain: trading` + documents a dead-end or failed approach | PROMOTE → 50_postmortems/ |
| `confidence: low` + has "What to read instead" | STUB (no action) |
| `confidence: low` + no "What to read instead" + source superseded | DELETE candidate |
| Multiple notes pointing at same principle | PROMOTE → 10_patterns/ (synthesize) |
| `domain: acquisition` + concrete deal structure or operator | CROSS-LINK → acquisition hub |
| Company-brain consulting artifact | PROMOTE → 50_company_brain/ |
| Skill-authoring post with novel principle not in skill-authoring-lessons.md | CROSS-LINK → 10_patterns/skill-authoring-lessons.md |
| Same content as existing inbox note, different filename | DELETE (duplicate) |
| Depends on future event or data | HOLD (with named trigger) |
