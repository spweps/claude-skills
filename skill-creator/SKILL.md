---
name: skill-creator
description: Create new skills, modify and improve existing skills, and measure skill performance. Use when users want to create a skill from scratch, edit, or optimize an existing skill, run evals to test a skill, benchmark skill performance with variance analysis, or optimize a skill's description for better triggering accuracy.
---

# Skill Creator

A skill for creating new skills and iteratively improving them.

**The loop:**
1. Decide what the skill should do
2. Write a draft
3. Create test prompts and run Claude-with-skill on them
4. Evaluate results qualitatively and quantitatively
5. Rewrite based on feedback
6. Repeat until satisfied
7. Expand test set and run at larger scale

Jump in wherever the user is in this process.

---

## Communicating with the user

Users range from non-technical to expert. Read context cues. "Evaluation" and "benchmark" are borderline OK. "JSON" and "assertion" need context before using without explanation. Brief definitions are fine when in doubt.

---

## Creating a skill

### Capture Intent

If the current conversation already contains a workflow to capture, extract it — tools used, sequence, corrections, input/output formats. Fill gaps with the user.

Four questions to answer before writing:
1. What should this skill enable Claude to do?
2. When should it trigger? (what phrases/contexts)
3. What's the expected output format?
4. Should we set up test cases?

Skills with objectively verifiable outputs (file transforms, data extraction, code generation) benefit from test cases. Skills with subjective outputs (writing style, art) often don't need them.

### Interview and Research

Ask about edge cases, input/output formats, example files, success criteria, dependencies. Wait to write test prompts until this is settled.

Check available MCPs for research. Research in parallel via subagents if available.

### Write the SKILL.md

Components:
- **name**: skill identifier (kebab-case, no `claude` or `anthropic`)
- **description**: when to trigger AND what it does. This is the primary triggering mechanism — all "when to use" info goes here, not in the body. **Make descriptions slightly "pushy"** — Claude undertriggers by default. "Use this skill whenever the user mentions X" rather than "For X tasks."
- **compatibility**: required tools/dependencies (optional, rarely needed)
- **body**: the rest of the skill

### Anatomy of a Skill

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (name, description required)
│   └── Markdown instructions
└── Bundled Resources (optional)
    ├── scripts/    - Executable code for deterministic/repetitive tasks
    ├── references/ - Docs loaded into context as needed
    └── assets/     - Files used in output (templates, icons, fonts)
```

### Progressive Disclosure

Three loading levels:
1. **Metadata** (name + description) — always in context (~100 tokens)
2. **SKILL.md body** — in context when skill triggers (<500 lines ideal)
3. **Bundled resources** — loaded on demand (unlimited)

Key patterns:
- Keep SKILL.md under 500 lines; add hierarchy and references if approaching limit
- Reference files clearly with guidance on when to read them
- For large reference files (>300 lines), include a table of contents
- Multi-domain skills: organize by variant in `references/` (e.g., `references/aws.md`, `references/gcp.md`)

### Writing Patterns

Use imperative form. Define output formats as exact templates. Explain *why* behind instructions — LLMs respond better to rationale than MUST/NEVER. Write the draft, then read it with fresh eyes and improve.

Output format example:
```markdown
## Report structure
ALWAYS use this exact template:
# [Title]
## Executive summary
## Key findings
## Recommendations
```

### Security

Skills must not contain malware, exploit code, or content that could compromise system security. Don't create misleading skills or skills designed for unauthorized access or data exfiltration.

---

## Running and evaluating test cases

Do not stop partway through this sequence.

Put results in `<skill-name>-workspace/` as a sibling to the skill directory. Organize by iteration (`iteration-1/`, `iteration-2/`), each test case gets a directory (`eval-0/`, `eval-1/`). Create directories as you go.

### Step 1: Spawn all runs in the same turn

For each test case, spawn two subagents simultaneously — one with-skill, one baseline (no skill for new skills, old-skill snapshot for improvements).

For improvements: snapshot the old skill first (`cp -r <skill-path> <workspace>/skill-snapshot/`).

Create `eval_metadata.json` per test case:
```json
{
  "eval_id": 0,
  "eval_name": "descriptive-name",
  "prompt": "The user's task prompt",
  "assertions": []
}
```

### Step 2: Draft assertions while runs are in progress

Good assertions are objectively verifiable with descriptive names. Subjective skills get qualitative review, not assertions.

Update `eval_metadata.json` and `evals/evals.json` with assertions once drafted.

### Step 3: Capture timing data from completion notifications

Each subagent completion notification contains `total_tokens` and `duration_ms`. Save immediately to `timing.json`:
```json
{
  "total_tokens": 84852,
  "duration_ms": 23332,
  "total_duration_seconds": 23.3
}
```
This data is only available in the completion notification — capture it or lose it.

### Step 4: Grade, aggregate, launch viewer

1. Grade each run — spawn a grader subagent reading `agents/grader.md`. Save to `grading.json`. Use `text`, `passed`, `evidence` fields (not `name`/`met`/`details`).

2. Aggregate:
```bash
python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
```
Produces `benchmark.json` and `benchmark.md`.

3. Analyst pass — read `agents/analyzer.md` for patterns: non-discriminating assertions, high-variance evals, time/token tradeoffs.

4. Launch viewer:
```bash
nohup python <skill-creator-path>/eval-viewer/generate_review.py \
  <workspace>/iteration-N \
  --skill-name "my-skill" \
  --benchmark <workspace>/iteration-N/benchmark.json \
  > /dev/null 2>&1 &
VIEWER_PID=$!
```

For Cowork/headless: use `--static <output_path>` for a standalone HTML file.

Tell the user: "Results are open in your browser. 'Outputs' tab for per-test feedback, 'Benchmark' tab for quantitative comparison. Come back when done."

### Step 5: Read feedback

```json
{
  "reviews": [
    {"run_id": "eval-0-with_skill", "feedback": "chart is missing axis labels", "timestamp": "..."}
  ],
  "status": "complete"
}
```

Empty feedback = user thought it was fine. Focus on specific complaints.

Kill viewer when done: `kill $VIEWER_PID 2>/dev/null`

---

## Improving the skill

### How to think about improvements

1. **Generalize from feedback** — you're building a skill that will run a million times, not just on these examples. Avoid overfitting to the test cases.
2. **Keep the prompt lean** — remove things not pulling their weight. Read transcripts, not just outputs — if the model wastes time on unproductive actions, cut the instructions causing them.
3. **Explain the why** — LLMs respond to rationale, not just imperatives. Understand what the user actually wants and transmit that understanding into the instructions.

### Description optimization

After the skill is working, run the description improver script to optimize triggering accuracy. The description is the most load-bearing line — it determines whether the skill triggers at all.

---

## Security Audit

Source: `https://github.com/anthropics/skills` — Anthropic first-party. Audit: PASS.

## Note on this installation

The official skill-creator includes a full eval-viewer UI (`eval-viewer/generate_review.py`), grader/analyzer subagent definitions (`agents/`), and aggregation scripts (`scripts/`). This SKILL.md captures the workflow; the scripts live in the source repo. For the complete toolchain, install via Claude Code: `/plugin install example-skills@anthropic-agent-skills`.
