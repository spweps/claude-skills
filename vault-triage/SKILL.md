---
name: vault-triage
description: Survey the vault inbox and produce a structured triage report: which notes to promote, cross-link, stub, delete, or leave. Use when the user says "triage the vault," "triage the inbox," "clean up the inbox," "what needs to be promoted," or "vault review." Also activates when the user asks what's accumulated since the last triage pass.
---

# Vault Triage Skill

## Purpose

The inbox is a staging area, not a filing system. Notes that sit in `00_inbox/` indefinitely stop compounding. This skill surveys the accumulated inbox, applies a decision rubric to each note, and produces a triage report — a structured action list the user can execute note by note.

Default mode: **report only**. The skill reads, analyzes, and outputs a markdown report. It does not move files, patch links, or delete notes unless the user explicitly enables write mode after reviewing the report.

## When this skill activates

Activate on any of:

- "Triage the vault" / "triage the inbox" / "clean up the inbox"
- "What needs to be promoted?"
- "What's accumulated since last pass?"
- "Vault review"
- User asks which inbox notes are relevant to a specific domain or project

Do NOT activate on:
- Requests to extract or process a specific post (use `post-extractor`)
- Requests to update a specific hub note or patterns note (do that directly)
- General questions about vault contents

## Vault structure reference

```
00_inbox/          Raw extractions. Staging only.
10_patterns/       Synthesized standing principles. Cross-source, cross-session.
20_domains/        Project hub notes by domain (trading/, acquisition/, infra/, revenue/).
30_skills_draft/   Skills in progress. Not yet validated.
40_skills_published/ Live skills. Mirrored to claude.ai.
50_*/              Standalone projects (one folder per project: 50_company_brain/, 50_postmortems/, etc.)
90_meta/           CLAUDE.md, session summaries, triage reports.
```

For the full decision rubric applied to each note, see `references/triage-rubric.md`.

## Inputs

**Full triage** — user says "triage the vault" with no qualifier. Survey all of `00_inbox/` and produce a complete report.

**Domain-scoped triage** — user specifies a domain ("triage the trading notes"). Filter inbox by `domain:` frontmatter field before applying the rubric.

**Date-scoped triage** — user specifies a date range ("triage everything since May 29"). Filter by `date_captured:` before applying the rubric.

**Incremental triage** — user says "what's new since last triage." Load the most recent triage report from `90_meta/` and compare `date_captured:` of inbox notes against the report's cutoff date. Process only newer notes.

## Process

### Step 0: Load context before triaging

Before reading any inbox note, load:
1. All hub notes in `20_domains/` (scan for existing `[[wiki-links]]` to identify already-linked notes)
2. All files in `10_patterns/` (identify which patterns notes exist and their `derived_from:` fields)
3. The most recent triage report in `90_meta/` if one exists (for incremental mode)

This context prevents recommending cross-links that already exist and avoids re-triaging already-processed notes.

### Step 1: Chunk the inbox

`00_inbox/` can accumulate 100+ notes between triage passes. Do not attempt to read all files in one pass — token limits make this unreliable.

Chunking protocol:
1. List all files in `00_inbox/` and count them.
2. Report the count to the user: "Inbox: N notes. Processing in batches of 20."
3. Process 20 notes per batch. After each batch, checkpoint: "Batch 1/N complete. Continue?"
4. If the user says yes, proceed to the next batch.
5. Assemble the full report only after all batches complete.

For each note in the batch: read the file, apply the triage rubric (see `references/triage-rubric.md`), assign a decision, and add to the report accumulator.

### Step 2: Apply the rubric

For each note, assign exactly one primary decision from the seven buckets:

| Decision | Trigger |
|---|---|
| **PROMOTE → 10_patterns/** | Note synthesizes a cross-source, reusable pattern or principle |
| **PROMOTE → 20_domains/{domain}/** | Note is project-specific reference a hub note should link |
| **PROMOTE → 50_{project}/** | Note belongs to a standalone project folder (50_postmortems/, 50_company_brain/, etc.) |
| **CROSS-LINK ONLY** | Note stays in inbox but should be linked from a hub or patterns note |
| **STUB** | Full extraction already done; note is slop with no value beyond the stub |
| **DELETE** | Exact duplicate of another note, or zero-value stub with no "what to read instead" |
| **HOLD** | Not ready — needs more context, future follow-up, or active dry-run data before actionable |

A note can have a secondary action in addition to its primary decision (e.g., PROMOTE + CROSS-LINK).

For full rubric criteria and edge cases, see `references/triage-rubric.md`.

### Step 3: Produce the report

Output a structured markdown report. See Output Format below.

After producing the report, ask: "Write this report to `90_meta/triage-YYYY-MM-DD.md`?"

## Output format

The triage report has five sections:

```markdown
# Vault Triage Report — {date}

**Inbox count:** {N} notes
**Date range:** {earliest} → {latest}
**Domains represented:** trading ({n}), acquisition ({n}), infra ({n}), ai ({n}), other ({n})

---

## Structural actions (do these first)

Vault-level issues that affect the filing system itself, not individual notes. Examples: folder renames, missing hub notes, orphaned folders, architecture decisions.

- {action}

---

## Promote to 10_patterns/

Notes that synthesize cross-source principles ready for the patterns layer.

| Note | Why promote | Suggested patterns note | New or extend? |
|---|---|---|---|
| [[note-slug]] | {reason} | {target} | new / extend |

---

## Promote to 20_domains/ or 50_{project}/

Project-specific notes that belong in a domain hub or standalone project folder.

| Note | Target location | Action for hub note |
|---|---|---|
| [[note-slug]] | 20_domains/trading/ | Add [[link]] to polybot.md under Related |

---

## Cross-link patches

Notes that stay in inbox but need a wiki-link added to a hub or patterns note.

| Note | Link to add | Target file | Section |
|---|---|---|---|
| [[note-slug]] | [[note-slug]] | polybot.md | Related notes |

---

## Delete candidates

Notes safe to delete. Reason required for each.

| Note | Reason | Superseded by |
|---|---|---|
| [[note-slug]] | Exact duplicate | [[other-note]] |

---

## Hold

Notes not ready for action. Reason and trigger condition for revisiting.

| Note | Reason | Revisit when |
|---|---|---|
| [[note-slug]] | Dry-run data needed | polybot exits paper-trading phase |

---

## Stats

- Promote to patterns: {n}
- Promote to domains/projects: {n}
- Cross-link only: {n}
- Delete: {n}
- Hold: {n}
- No action needed: {n}
```

## What NOT to do

- Do not move, rename, or delete any file without explicit user confirmation.
- Do not re-triage notes already processed in a prior triage report (check the report date).
- Do not assign PROMOTE to a note that is a stub — stubs are already appropriately classified.
- Do not recommend promoting every note — most inbox notes are appropriately parked there. Promote only when there is a clear, specific destination.
- Do not collapse the report into prose. Tabular format is required — it is the action list the user will execute row by row.
- Do not skip the chunk checkpoint. Triaging 141 notes in one pass is unreliable. Batch and confirm.

## Behaviors

### 1. Structural actions come first

Before triaging individual notes, surface any vault-level structural issues: orphaned folders, missing hub notes for active domains, folder naming inconsistencies. These affect where notes get promoted and should be resolved first.

Example structural actions:
- Confirm all `50_` project folders use underscores (not hyphens)
- Create `50_postmortems/` if it doesn't exist and postmortem-type notes are in the inbox
- Note if `30_skills_draft/` is empty — if skills are being published directly, the draft stage may be unused

### 2. Hub note cross-linking is the highest-leverage triage action

A note that stays in inbox but gets linked from a hub note is not lost — it's reachable via the graph. The highest-value triage action is often adding a `[[wiki-link]]` to the right hub note, not moving the file.

When reviewing each inbox note, check:
- Does any hub note in `20_domains/` have an "Open questions" or "Related notes" section this note would answer?
- Does this note directly address a `- [ ]` item in a hub note?
- Does this note supersede a HOLD item from a previous triage report?

### 3. Patterns promotion has a three-source threshold

Per `10_patterns/skill-authoring-lessons.md` Principle 7: a pattern earns its own note when three or more inbox notes point to the same observation. Do not promote a single note to patterns — synthesize when the threshold is met.

Exception: a single note that contains a complete, original framework (not derivative) may be promoted directly.

### 4. HOLD is not a dump bucket

Every HOLD entry must have a specific, actionable revisit condition — not "revisit later." If you can't name the condition, the note should be cross-linked or left as-is, not held.

Good HOLD conditions:
- "Revisit when polybot exits dry-run phase"
- "Revisit when GTA 6 launch date is confirmed"
- "Revisit when V2 CLOB calibration data is available"

Bad HOLD conditions:
- "Revisit later"
- "Needs more context"
- "Not sure yet"

### 5. Delete is rare and narrow

Delete only when:
1. The note is an exact duplicate (same source, same content, different filename)
2. The note is a zero-value stub with no "what to read instead" section and the source has been superseded by a better extraction already in the vault

Do not delete notes because they are low-quality. Low-quality stubs with a "what to read instead" still have negative-space value.

### 6. Report to `90_meta/` after user confirms

After producing the report in-chat, ask whether to write it to `90_meta/triage-YYYY-MM-DD.md`. On confirmation, write the file. This creates the audit trail that enables incremental triage in future sessions.

## Reference files

- **`references/triage-rubric.md`** — full decision criteria for each of the seven buckets, edge cases, and worked examples. Load during Step 2 when a note's classification is ambiguous.
