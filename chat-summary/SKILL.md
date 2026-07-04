---
name: chat-summary
description: Maintains a rolling markdown summary of the current chat that can be re-loaded at the start of future chats to restore full context without rereading transcripts. Activates whenever the user uploads or pastes a file matching the chat summary template (sections TL;DR, Objective, Context & Constraints, Timeline, Decisions Log, Code & Artifacts, Open Threads, Resolved, References), or asks to start/continue/update a chat summary. Auto-updates the summary at the end of every response once active. Trigger phrases include "chat summary", "update the summary", "here's the summary from our last chat", or any uploaded .md whose header reads `# Chat Summary:`.
---

# Chat Summary Skill

## Purpose

This skill maintains a comprehensive, rolling summary of a single chat in a single `.md` file. The user archives one file per chat. At the start of a new chat, they paste or upload the summary from a prior related chat so Claude can resume with full context without rereading the original transcript.

The goal is **token efficiency + continuity**: the summary is dense enough to replace the transcript but structured enough to be scannable and extendable.

## When this skill activates

Activate whenever any of these are true:

- The user uploads or pastes a markdown file beginning with `# Chat Summary:` or containing the template's section headers.
- The user says something like: "start a chat summary", "update the summary", "keep a running summary of this chat", "here's the summary from last time", "continue from this summary".
- The user references a prior chat and supplies a summary-formatted file.

Once activated, the skill stays active for the rest of the chat.

## The template

The canonical template lives at `CHAT_SUMMARY_TEMPLATE.md` in this skill directory. It has ten sections:

1. **TL;DR** — one paragraph, rewritten every update
2. **Objective** — goal, why, done-when
3. **Context & Constraints** — environment, tools, preferences, hard requirements
4. **Timeline (Narrative)** — dated, chronological log of what happened
5. **Decisions Log** — append-only table: date, decision, rationale, superseded-by
6. **Code & Artifacts** — every code block produced, with filename, date, purpose, full code
7. **Open Threads** — unresolved questions/bugs/ideas
8. **Resolved / Closed** — things moved out of Open Threads
9. **References** — external links, prior chats, repos
10. **Glossary** (optional) — project-specific terms

Always read `CHAT_SUMMARY_TEMPLATE.md` once at the start of the chat when this skill activates, so the exact structure is fresh.

## Core behaviors

### 1. Auto-update every response (once active)

At the end of **every** assistant response after activation, append a full updated summary to the response inside a fenced markdown code block. The user saves this over their archive. Specifically:

- Rewrite sections 1 (TL;DR), 3 (Context & Constraints if changed), and update the `Last updated` field.
- Append new entries to sections 4 (Timeline), 5 (Decisions Log), 6 (Code & Artifacts), 7 (Open Threads).
- Move items between 7 (Open) and 8 (Resolved) as they close.
- Never delete history from sections 4, 5, 6 — they are append-only.

### 2. Preserve, do not overwrite

Timeline, Decisions, and Code & Artifacts are **append-only**. If code is revised, add a new dated block alongside the old one — do not replace it. If a decision is reversed, add a new decision entry and fill in the `Superseded by` column on the old one. This preserves the full record of what happened and why, which is the whole point.

### 3. Dates are mandatory

Every Timeline entry, Decision, and Code & Artifacts block gets a date in `YYYY-MM-DD` format. Use the current date provided in the system context. Include `HH:MM` on Timeline entries if multiple things happen in one day.

### 4. TL;DR is the load-bearing section

The TL;DR is what gets read first when the summary is pasted into a future chat. It must answer, in one paragraph: what is this chat about, what has been built/decided, and what is the current state. Rewrite it fully every update — do not just append.

### 5. Code blocks go in full

Section 6 captures **complete** code, not snippets. If a file has been produced in the chat, include the full current version under its filename heading, with the date. If the code changes later, add a new entry with the new date — keep the old one for history.

### 6. Open Threads are checkboxes

Use `- [ ]` for open, `- [x]` for resolved. When resolving, move the item to section 8 with a note on how it was resolved.

### 7. When resuming from a prior summary

If the user opens a chat by pasting a summary:

1. Acknowledge you've loaded the context (briefly — don't recite the whole thing back).
2. Confirm the current state in 1–2 sentences based on the TL;DR and latest Timeline entry.
3. Ask what they want to work on now, or proceed if they've already said.
4. Update the `Last updated` timestamp and add a new Timeline entry noting the resume.

### 8. Compaction-under-pressure warning

As the chat approaches token budget limits, there is pressure to silently drop content from the summary to keep it short. **This must not happen without surfacing it.**

If the summary is growing large enough that the next update would require dropping or truncating prior content to fit in a single response:

1. State this explicitly before producing the update: "The summary is approaching a size where I'll need to compress prior sections. Flagging this before compacting."
2. Compact only sections 4 (Timeline) and 6 (Code & Artifacts) — and only by summarizing older entries, not deleting them. Never compact sections 5 (Decisions Log), 7 (Open Threads), or 8 (Resolved).
3. Mark compacted entries with `[compacted YYYY-MM-DD]` so future-you knows what happened.
4. If the summary is already so large that even a compacted version won't fit, recommend archiving: "Suggest archiving this summary and starting a fresh one for the next phase, with a References entry pointing back."

The failure mode this prevents: arriving at the end of a long session with a summary that silently omits the last hour of work because the skill ran out of space and didn't say so.

### 9. Discipline check — when you want to skip the update

The temptation to skip the auto-update is highest exactly when it matters most: long responses, near token budget, "minor" changes that feel not worth the overhead.

| Temptation to skip | Why it's wrong | Correct action |
|---|---|---|
| "This response was short, nothing important changed" | Open Threads and Timeline still need the entry | Update anyway; it takes 10 lines |
| "We're near token budget, the summary will be long" | This is when compaction logic (behavior 8) applies | Surface the pressure, compact correctly, don't skip |
| "The user didn't ask for a summary update" | Auto-update means every response, not on-request | Update at end of response as usual |
| "I'll do a big update at the end of the session" | Sessions end abruptly; the last update is the one that matters | Update incrementally, every response |
| "The summary already has this information" | TL;DR needs rewriting; timeline needs the new entry | Rewrite TL;DR, append timeline entry minimum |

## Output format for the auto-update

At the end of each response, after your normal reply content, add:

````
---

**Updated chat summary** — save over your archive:

```markdown
# Chat Summary: [topic]

**Chat started:** ...
**Last updated:** ...
[full updated summary here]

<!-- END OF SUMMARY -->
```
````

Keep the summary in a fenced code block so the user can copy it cleanly.

## What NOT to do

- **Do not** produce a summary when the chat is clearly a one-off question (weather, quick lookup, casual chat). This skill is for substantive, multi-turn work.
- **Do not** silently drop old code, decisions, or timeline entries to save space. If the summary grows long, that is expected — it is replacing a transcript.
- **Do not** editorialize in the Timeline. It is a factual log, not commentary.
- **Do not** include sensitive data (API keys, credentials, private keys) in the summary. If they come up in chat, note "credential discussed, not recorded" in the Timeline instead.
- **Do not** rewrite the Decisions Log or Timeline retroactively. Append corrections as new entries.

## Length discipline

The summary will grow. That is the point. But:

- TL;DR stays to one paragraph (~4–6 sentences).
- Timeline entries stay to 1–3 sentences each.
- Context & Constraints stays tight — it is reference, not narrative.
- Code blocks are as long as they need to be. No truncation.

If the summary becomes so long it is itself expensive to reload, suggest to the user that they archive the current file and start a fresh summary for a new phase of work, with a `References` entry pointing back to the archived file.
