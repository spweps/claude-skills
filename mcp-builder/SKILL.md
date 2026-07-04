---
name: mcp-builder
description: Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. Use when building MCP servers to integrate external APIs or services, whether in Python (FastMCP) or Node/TypeScript (MCP SDK).
license: Complete terms in LICENSE.txt
---

# MCP Server Development Guide

Create MCP servers that enable LLMs to interact with external services through well-designed tools. Quality is measured by how well the server enables LLMs to accomplish real-world tasks.

**Recommended stack:** TypeScript + Streamable HTTP (remote) or stdio (local).

---

## Phase 1: Deep Research and Planning

### 1.1 Understand Modern MCP Design

**API Coverage vs. Workflow Tools:** Balance comprehensive coverage with specialized workflow tools. When uncertain, prioritize comprehensive API coverage.

**Tool naming:** Use service prefix + action verb. `github_create_issue` not `create_issue`. Clear, descriptive, action-oriented.

**Context management:** Design tools that return focused, relevant data. Support filtering and pagination.

**Error messages:** Guide agents toward solutions with specific suggestions and next steps.

### 1.2 Study MCP Protocol Documentation

Start with `https://modelcontextprotocol.io/sitemap.xml`, then fetch specific pages with `.md` suffix.

Key areas: specification overview, transport mechanisms (streamable HTTP, stdio), tool/resource/prompt definitions.

### 1.3 Load Framework Documentation

**TypeScript SDK:** WebFetch `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`

**Python SDK:** WebFetch `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`

**Best practices:** Read `reference/mcp_best_practices.md`

**Language guides:**
- TypeScript: `reference/node_mcp_server.md`
- Python: `reference/python_mcp_server.md`

### 1.4 Plan Implementation

Review the target API's documentation. List endpoints to implement, starting with most common operations.

---

## Phase 2: Implementation

### 2.1 Project Structure

See language-specific guides (`reference/node_mcp_server.md` or `reference/python_mcp_server.md`).

### 2.2 Core Infrastructure

- API client with authentication
- Error handling helpers with actionable messages
- Response formatting (JSON/Markdown)
- Pagination support

### 2.3 Tool Implementation

For each tool:

**Input schema:** Use Zod (TypeScript) or Pydantic (Python). Include constraints and clear descriptions. Add examples in field descriptions.

**Output schema:** Define `outputSchema` where possible. Use `structuredContent` in responses (TypeScript SDK).

**Tool description:** Concise summary + parameter descriptions + return type.

**Annotations:**
- `readOnlyHint`: true/false
- `destructiveHint`: true/false
- `idempotentHint`: true/false
- `openWorldHint`: true/false

Note: annotations are hints, not security guarantees.

---

## Phase 3: Review and Test

### 3.1 Code Quality

- No duplicated code
- Consistent error handling
- Full type coverage
- Clear tool descriptions

### 3.2 Build and Test

**TypeScript:** `npm run build` → `npx @modelcontextprotocol/inspector`

**Python:** `python -m py_compile your_server.py` → MCP Inspector

---

## Phase 4: Evaluations

Load `reference/evaluation.md` for complete guidelines.

Create 10 evaluation questions. Each must be:
- Independent (not dependent on other questions)
- Read-only (non-destructive operations only)
- Complex (requires multiple tool calls)
- Realistic (based on real use cases)
- Verifiable (single, clear, stable answer)

Output format:

```xml
<evaluation>
  <qa_pair>
    <question>Complex realistic question requiring multiple tool calls</question>
    <answer>Specific verifiable answer</answer>
  </qa_pair>
</evaluation>
```

---

## Reference Files

Load as needed:

- `reference/mcp_best_practices.md` — naming conventions, response formats, pagination, security
- `reference/node_mcp_server.md` — TypeScript project structure, Zod schemas, tool registration, examples
- `reference/python_mcp_server.md` — Python/FastMCP patterns, Pydantic models, tool registration, examples
- `reference/evaluation.md` — eval creation guide, XML format, running scripts

## Security Audit

Source: `https://github.com/anthropics/skills` — Anthropic first-party. Audit: PASS.
