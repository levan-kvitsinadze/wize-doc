---
name: wize-doc
description: Generate business-focused documentation for features by analyzing code, Linear (official Linear MCP), and Notion (official Notion MCP). Use when the user requests documentation for a specific feature (e.g. "/document auto-restock"). Focuses on business rules, workflows, and business context. Saves markdown to the repo under wize-back/Documentation/features/. Requires Linear and Notion MCP to be authenticated (OAuth in Cursor/Claude).
---

# Wize Doc — feature documentation

Generate business-focused documentation with **business rules** as the primary depth.

## MCP servers

Use the **hosted Linear MCP** (`https://mcp.linear.app/mcp`) and **hosted Notion MCP** (`https://mcp.notion.com/mcp`). Tool names below match typical clients; yours may be **prefixed** (e.g. `mcp_linear_*`, `mcp_notion_*`) — use the tools exposed for the same purpose.

| Goal | Linear (typical) | Notion (typical) |
|------|------------------|------------------|
| Search | `list_issues` with `query` (and optional `team`, `project`, etc.) | `notion-search` |
| Fetch one item | `get_issue` with issue id (e.g. `WIZ-123`) | `notion-fetch` with page URL or id |

Refer to [Notion supported tools](https://developers.notion.com/docs/mcp-supported-tools) for additional Notion tools if needed (e.g. child blocks via fetch).

## Repo paths (Workwize)

- **Existing docs:** `wize-back/Documentation/features/*.md`
- **Save new/updated doc:** `wize-back/Documentation/features/<kebab-case-feature>.md`

If the workspace layout differs, resolve the backend root from the user or project (e.g. env `WIZE_BACK_PATH`) and keep `Documentation/features/` under it.

**Kebab-case filename:** lowercase, non-alphanumerics → `-`, trim edge dashes (e.g. `Auto Restock` → `auto-restock.md`).

## Platform: interactive questions

For Linear/Notion choice flows use your product’s multi-choice UI:

- **Cursor:** `AskUserQuestion` where applicable.
- **Claude Code:** equivalent interactive prompts or chat with the same options.

## Getting started

1. Extract the **feature name** from the user message.
2. Start with **check existing docs** — do not skip.
3. Run steps **in order**.

## Workflow

### 1. Check existing documentation

Use workspace tools (no documentation MCP):

- **Glob** (or equivalent) on `wize-back/Documentation/features/*.md` (or under resolved backend path).
- Treat files as matching the feature if the normalized feature name matches the filename stem (same fuzzy idea: exact, substring either way — ignore `.md`, case-insensitive, strip non-alphanumeric for comparison).

If matches exist: show titles/paths and ask whether to **update/overwrite** before replacing.

### 2. Gather business context — Linear

Ask (multi-choice):

- Search issues for this feature / Provide issue ids / Skip Linear

**Search:** call Linear MCP **list/search** tools — typically `list_issues` with `query` set to the feature name (adjust filters if the user specifies team/project).

**Provide ids:** ask for space-separated identifiers, then `get_issue` per id.

**Skip:** no Linear calls.

### 3. Gather business context — Notion

Ask (multi-choice):

- Search workspace / Provide page URLs or ids / Skip Notion

**Search:** `notion-search` with a query derived from the feature name.

**Provide URLs/ids:** `notion-fetch` per page. Include nested content as returned by fetch (follow up with additional fetches if the model needs child pages).

**Skip:** no Notion calls.

### 4. Discover code files

Use file search (e.g. **Glob**) in parallel:

- `**/*<feature-token>*.php`
- `**/*<feature-token>*.{ts,tsx}`

Show results; select **10–15** most relevant files; user may add paths. Use Linear/Notion context to widen/narrow selection.

### 5. Fetch sources

- **Code:** **Read** each selected file.
- **Linear:** `get_issue` (or equivalent) for each chosen issue.
- **Notion:** `notion-fetch` for each chosen page.

### 6. Analyze and write the document

Same priorities as before:

1. **Business rules** (validations, conditions, calculations, states, limits)
2. Purpose, workflows, stakeholders
3. Light **technical summary** only

If information is missing, ask the user before finalizing.

Use this structure:

```markdown
# Feature Name

## Overview

## Business Context
- **Business Value**:
- **User Problems Solved**:
- **Key Stakeholders**:

## User Workflows

### Primary Workflow

### Alternative Workflows

## Business Rules

### Validations
### Conditions & Logic
### Calculations
### State Transitions
### Constraints & Limits
### Data Integrity Rules

## Technical Summary

### Key Dependencies

## Related Resources
- Linear:
- Notion:
- Related Docs:
```

**No Mermaid** — plain text only.

### 7. Save documentation

Write the file with the workspace **Write** (or **Edit**) tool:

- Path: `wize-back/Documentation/features/<kebab-case>.md`
- Create parent directories if needed.

Optional: after saving, offer to create/update a **Notion** page via `notion-create-pages` / `notion-update-page` only if the user asks.

### 8. Confirm

Return the **absolute or workspace-relative path** written and suggest a quick human review.

## Principles

- **Clarity over assumption**
- **Audience:** PMs, analysts, stakeholders
- **Depth:** business rules first; minimal implementation dumps
- **Structure:** consistent template; **Business Rules** is the richest section

## Example trace

1. Glob features dir → no `auto-restock.md`
2. Linear: user provides `WIZ-1` → `get_issue`
3. Notion: skip
4. Glob code for `auto-restock`
5. Read selected PHP/TS files
6. Draft markdown
7. Write `wize-back/Documentation/features/auto-restock.md`
8. Confirm path
