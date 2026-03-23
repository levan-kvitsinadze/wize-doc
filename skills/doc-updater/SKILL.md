---
name: doc-updater
description: Revise existing feature documentation using a change description and optional new context (code analysis, Linear, Notion). Preserves unchanged sections verbatim. Used internally by the /document command update flow.
---

# Doc Updater

Revise an existing feature documentation file based on what changed.

## Input

From the command:
- Existing doc content and file path
- User's description of what changed
- Code analysis (optional — only if user chose to re-explore)
- New Linear issues (optional)
- New Notion pages (optional)

## Steps

### 1. Read and parse the existing doc

Read the existing doc in full. Identify the sections present (headings and their content).

### 2. Determine affected sections

Based on the user's change description and any new context provided, identify which sections need updating:

- Changes to business rules → update **Business Rules**
- New user-facing behavior → update **User Workflows** and/or **Overview**
- New dependencies or components → update **Technical Summary**
- Changes to purpose or value → update **Business Context**
- Any combination of the above

Sections not touched by the change should be preserved verbatim.

### 3. Rewrite affected sections

For each affected section:
- Incorporate the new information from the change description and any provided context
- Keep the same markdown structure and heading levels
- Be specific — reflect the actual change, not generic rewrites
- If a new subsection is warranted (e.g. a new business rule category), add it in the appropriate place

If critical information is missing or the change description is ambiguous, ask the user before rewriting.

### 4. Save the updated doc

Write the complete updated document back to the same file path (overwrite).

Keep all unaffected sections exactly as they were.

### 5. Summarize changes

Report back:
- File path saved
- List of sections that were updated
- Briefly what changed in each

### 6. Offer Notion sync (optional)

If the doc's **Related Resources** section references a Notion page, offer once:

> "Doc updated at `<path>`. Want me to also update the Notion page?"

Only proceed if the user confirms. Use `notion-update-page` with the existing page ID.
