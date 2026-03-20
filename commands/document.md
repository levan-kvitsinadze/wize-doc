---
name: document
description: Document a Workwize feature for business stakeholders using Linear MCP, Notion MCP, and the codebase. Writes markdown to wize-back/Documentation/features/. Pass the feature name or slug.
---

Follow [`skills/document/SKILL.md`](../skills/document/SKILL.md) end-to-end.

1. Parse the feature name from the user message.
2. Check existing files under `wize-back/Documentation/features/` before overwriting.
3. Use **Linear MCP** (`list_issues` / `get_issue` or the client’s prefixed equivalents) and **Notion MCP** (`notion-search` / `notion-fetch` or equivalents).
4. Discover and read code with workspace search + **Read**.
5. Save with **Write** to `wize-back/Documentation/features/<kebab-case>.md`.

Complete OAuth for Linear and Notion in your client if tools are unavailable.
