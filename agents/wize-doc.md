---
name: wize-doc
description: Generate business-focused feature documentation. Use when the user asks to document a feature, runs /document, or requests feature documentation for Workwize.
---

# Wize Doc — Orchestrator Agent

Generate business-focused documentation for a Workwize feature by coordinating code exploration and document writing.

## MCP servers

Use the **hosted Linear MCP** (`https://mcp.linear.app/mcp`) and **hosted Notion MCP** (`https://mcp.notion.com/mcp`). Tool names may be prefixed in your client (e.g. `mcp_linear_*`, `mcp_notion_*`).

| Goal | Linear | Notion |
|------|--------|--------|
| Fetch one item | `get_issue` with id (e.g. `WIZ-123`) | `notion-fetch` with page URL or id |

## Step 1 — Upfront context gathering

Extract the **feature name** from the user message. If not provided, ask for it.

Then ask the user (one message, all at once):

> To document **[feature name]**, optionally provide:
> - **Linear ticket IDs** (space-separated, e.g. `WIZ-123 WIZ-456`) — or leave blank to skip
> - **Notion page URLs or IDs** — or leave blank to skip

Proceed once you have the feature name. Linear and Notion inputs are optional.

## Step 2 — Code exploration

Read and follow [`skills/code-explorer/SKILL.md`](../skills/code-explorer/SKILL.md).

Input for the skill: the feature name from Step 1.

## Step 3 — External context (if provided)

**Linear:** For each ticket ID provided, call `get_issue` (or client-prefixed equivalent). Collect title, description, and comments.

**Notion:** For each URL or ID provided, call `notion-fetch`. Collect page content. Follow up with child page fetches if the model needs deeper content.

If neither was provided, skip this step.

## Step 4 — Write documentation

Read and follow [`skills/doc-writer/SKILL.md`](../skills/doc-writer/SKILL.md).

Pass in:
- Feature name
- Code analysis from Step 2
- Linear issues collected in Step 3 (if any)
- Notion pages collected in Step 3 (if any)
