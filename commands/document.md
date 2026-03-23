---
name: document
description: Create or update business-focused feature documentation. Explores the codebase and optionally uses Linear and Notion context. Writes markdown to wize-back/Documentation/features/. Pass the feature name or slug.
allowed-tools: Glob, Read, Write
---

# Document — Orchestrator

Generate or update business-focused documentation for a Workwize feature.

## MCP servers

Use the **hosted Linear MCP** (`https://mcp.linear.app/mcp`) and **hosted Notion MCP** (`https://mcp.notion.com/mcp`). Tool names may be prefixed in the client (e.g. `mcp_linear_*`, `mcp_notion_*`).

## Step 1 — Identify the feature

Extract the feature name from `$ARGUMENTS`. If not provided, ask for it before continuing.

## Step 2 — Check for existing documentation

Glob `wize-back/Documentation/features/*.md`.

Normalize both the feature name and each filename (lowercase, strip non-alphanumeric chars) and check for a match (exact or substring either way).

- **No match found** → proceed to [Create flow](#create-flow).
- **Match found** → tell the user the path of the existing doc and ask:
  - "Update existing doc" → proceed to [Update flow](#update-flow).
  - "Recreate from scratch" → proceed to [Create flow](#create-flow).

---

## Create flow

### C1 — Gather external context (optional)

Ask the user in a single message:

> To document **[feature name]**, optionally provide:
> - **Linear ticket IDs** (space-separated, e.g. `WIZ-123 WIZ-456`) — or leave blank to skip
> - **Notion page URLs or IDs** — or leave blank to skip

Proceed once the user responds (blank is fine).

### C2 — Code exploration

Read and follow `skills/code-explorer/SKILL.md`.

Input: the feature name.

### C3 — Fetch external context (if provided)

**Linear:** For each ticket ID, call `get_issue`. Collect title, description, and comments.

**Notion:** For each URL or ID, call `notion-fetch`. Follow child page fetches if deeper content is needed.

Skip this step if nothing was provided.

### C4 — Write documentation

Read and follow `skills/doc-writer/SKILL.md`.

Pass in:
- Feature name
- Code analysis from C2
- Linear issues from C3 (if any)
- Notion pages from C3 (if any)

---

## Update flow

### U1 — Read existing doc

Read the full content of the matched documentation file.

### U2 — Gather change context

Ask the user in a single message:

> 1. What changed or needs updating? (describe the change, new behavior, fixed rule, etc.)
> 2. Should I re-explore the codebase for this change, or update from the context you provide?
> 3. Any new Linear ticket IDs or Notion URLs to include? (or leave blank)

### U3 — Code exploration (if requested)

If the user chose to re-explore: read and follow `skills/code-explorer/SKILL.md` with the change description as the search focus.

Otherwise skip.

### U4 — Fetch new external context (if provided)

Same as C3 — fetch any new Linear/Notion references the user provided in U2.

Skip if none.

### U5 — Update documentation

Read and follow `skills/doc-updater/SKILL.md`.

Pass in:
- Existing doc content and path
- User's change description from U2
- Code analysis from U3 (if any)
- New Linear issues from U4 (if any)
- New Notion pages from U4 (if any)
