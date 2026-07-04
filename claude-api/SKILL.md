---
name: claude-api
description: Build apps with the Claude API or Anthropic SDK. TRIGGER when: code imports `anthropic`/`@anthropic-ai/sdk`/`claude_agent_sdk`, or user asks to use Claude API, Anthropic SDKs, or Agent SDK. DO NOT TRIGGER when: code imports `openai`/other AI SDK, general programming, or ML/data-science tasks.
license: Complete terms in LICENSE.txt
---

# Building LLM-Powered Applications with Claude

This skill helps you build LLM-powered applications with Claude. Choose the right surface based on your needs, detect the project language, then read the relevant language-specific documentation.

## Defaults

Unless the user requests otherwise:

- **Model**: Use `claude-opus-4-6` — non-negotiable default. Do not downgrade for cost.
- **Thinking**: Use `thinking: {type: "adaptive"}` for anything remotely complicated. `budget_tokens` is deprecated on Opus 4.6 and Sonnet 4.6 — never use it.
- **Streaming**: Default to streaming for any request with long input, long output, or high `max_tokens`. Use `.get_final_message()` / `.finalMessage()` if you don't need individual stream events.

## Language Detection

Before reading code examples, determine language from project files:

- `*.py`, `requirements.txt`, `pyproject.toml` → **Python** — read from `python/`
- `*.ts`, `*.tsx`, `package.json`, `tsconfig.json` → **TypeScript** — read from `typescript/`
- `*.js`, `*.jsx` (no `.ts` files) → **TypeScript** — JS uses same SDK
- `*.java`, `pom.xml`, `build.gradle` → **Java** — read from `java/`
- `*.go`, `go.mod` → **Go** — read from `go/`
- `*.rb`, `Gemfile` → **Ruby** — read from `ruby/`
- `*.cs`, `*.csproj` → **C#** — read from `csharp/`
- `*.php`, `composer.json` → **PHP** — read from `php/`

If ambiguous, ask. If unsupported (Rust, Swift, etc.), suggest cURL/raw HTTP from `curl/`.

### Language-Specific Feature Support

| Language | Tool Runner | Agent SDK |
|---|---|---|
| Python | Yes (beta) | Yes |
| TypeScript | Yes (beta) | Yes |
| Java | Yes (beta) | No |
| Go | Yes (beta) | No |
| Ruby | Yes (beta) | No |
| C# | No | No |
| PHP | No | No |

## Which Surface Should I Use?

**Start simple.** Default to the simplest tier that meets your needs.

| Use Case | Surface |
|---|---|
| Classification, summarization, extraction, Q&A | Claude API — single call |
| Batch processing or embeddings | Claude API — specialized endpoints |
| Multi-step pipelines with code-controlled logic | Claude API + tool use |
| Custom agent with your own tools | Claude API + tool use (manual loop) |
| AI agent with file/web/terminal access | Agent SDK |
| Want built-in permissions and guardrails | Agent SDK |

**Decision tree:**

```
Single LLM call? → Claude API
Claude needs to discover/access files, web, shell itself? → Agent SDK
Multi-step, code-orchestrated, your own tools? → Claude API + tool use
Open-ended agent, model controls trajectory? → Claude API agentic loop
```

Agent checklist before choosing agent tier: Complexity (multi-step, hard to specify)? Value (outcome justifies cost/latency)? Viability (Claude capable)? Error cost (recoverable)? If any answer is no, stay simpler.

## Architecture

Everything goes through `POST /v1/messages`. Tools and output constraints are features of this single endpoint.

- **User-defined tools** — define tools; SDK handles the loop
- **Server-side tools** — Anthropic-hosted (code execution, computer use)
- **Structured outputs** — use `output_config: {format: {...}}` (not deprecated `output_format`)
- **Supporting endpoints** — Batches, Files, Token Counting, Models API

## Current Models

| Model | ID | Context | Input $/1M | Output $/1M |
|---|---|---|---|---|
| Claude Opus 4.6 | `claude-opus-4-6` | 200K (1M beta) | $5.00 | $25.00 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | 200K (1M beta) | $3.00 | $15.00 |
| Claude Haiku 4.5 | `claude-haiku-4-5` | 200K | $1.00 | $5.00 |

**Use exact model ID strings as-is. Do not append date suffixes.**

For live capability lookup: `client.models.retrieve(id)` — see `shared/models.md`.

## Thinking & Effort

**Adaptive thinking (Opus 4.6, recommended):** `thinking: {type: "adaptive"}`. No `budget_tokens`. When user asks for "extended thinking" or a "thinking budget" — use adaptive thinking on Opus 4.6, do not switch to older models.

**Effort parameter (GA):** `output_config: {effort: "low"|"medium"|"high"|"max"}`. Default `high`. `max` is Opus 4.6 only. Works on Opus 4.5, Opus 4.6, Sonnet 4.6. Errors on Sonnet 4.5 / Haiku 4.5.

**Older models (explicit request only):** Use `thinking: {type: "enabled", budget_tokens: N}` where `budget_tokens < max_tokens` (min 1024).

## Compaction

Beta, Opus 4.6 and Sonnet 4.6. Header: `compact-2026-01-12`. Auto-summarizes context near 150K tokens.

**Critical:** Append `response.content` (not just text) on every turn. Compaction blocks must be preserved in the message array or the state is lost silently.

## Common Pitfalls

- **`budget_tokens` deprecated on 4.6** — use adaptive thinking instead; throws error if used
- **Opus 4.6 prefill removed** — assistant message prefills return 400; use `output_config.format` instead
- **`max_tokens` defaults** — non-streaming: ~16000; streaming: ~64000; don't lowball
- **128K output tokens** — requires streaming on Opus 4.6
- **Structured outputs** — use `output_config: {format: {...}}` not `output_format` (deprecated)
- **Tool input JSON** — always `json.loads()` / `JSON.parse()`; never raw string match on tool inputs
- **Don't reimplement SDK** — use `stream.finalMessage()`, typed exceptions, SDK types

## Reading Guide

After detecting language, read:

- Single call: `{lang}/claude-api/README.md`
- Streaming: + `{lang}/claude-api/streaming.md`
- Tool use / agents: + `shared/tool-use-concepts.md` + `{lang}/claude-api/tool-use.md`
- Batch: + `{lang}/claude-api/batches.md`
- File uploads: + `{lang}/claude-api/files-api.md`
- Agent SDK (Python/TypeScript only): `{lang}/agent-sdk/README.md` + `{lang}/agent-sdk/patterns.md`
- Error handling: `shared/error-codes.md`
- Live docs: `shared/live-sources.md`

## Security Audit

Source: `https://github.com/anthropics/skills` — Anthropic first-party. Audit: PASS.
