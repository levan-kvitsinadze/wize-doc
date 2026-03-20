---
name: doc-writer
description: Write business-focused feature documentation using code analysis and optional external context (Linear, Notion). Saves markdown to wize-back/Documentation/features/. Used internally by the wize-doc agent.
---

# Doc Writer

Write and save business-focused feature documentation.

## Input

From the orchestrator:
- Feature name
- Code analysis (from code-explorer)
- Linear issues (if any)
- Notion pages (if any)

## Steps

### 1. Check for existing documentation

Glob `wize-back/Documentation/features/*.md`.

Match against the feature name: normalize both sides (lowercase, strip non-alphanumeric) and check for exact match or substring match either way.

If a match is found: show the path and ask whether to **update/overwrite** before continuing.

### 2. Derive the output filename

Kebab-case: lowercase, non-alphanumeric characters → `-`, trim leading/trailing dashes.

Examples: `Auto Restock` → `auto-restock.md`, `Budget Approval Flow` → `budget-approval-flow.md`

Output path: `wize-back/Documentation/features/<kebab-case>.md`

### 3. Synthesize and write the document

Use all available context — code analysis, Linear issues, Notion pages — to populate the template below.

**Prioritize depth in this order:**
1. Business rules (most important — be thorough)
2. User workflows and purpose
3. Technical summary (keep light — no code dumps)

If critical information is missing (e.g. purpose unclear, no business rules extractable), ask the user before finalizing.

**No Mermaid diagrams** — plain text only.

Use this template:

```markdown
# Feature Name

## Overview

Brief description of what this feature does and why it exists.

## Business Context

- **Business Value**:
- **User Problems Solved**:
- **Key Stakeholders**:

## User Workflows

### Primary Workflow

Step-by-step description of the main user journey.

### Alternative Workflows

Other paths or edge cases (skip section if none).

## Business Rules

### Validations

What is validated, when, and what happens on failure.

### Conditions & Logic

Decision points and branching behavior.

### Calculations

Any computed values, formulas, or derived data.

### State Transitions

Status changes, lifecycle stages, trigger conditions.

### Constraints & Limits

Quantity limits, time limits, permission gates, quotas.

### Data Integrity Rules

Uniqueness constraints, required relationships, consistency rules.

## Technical Summary

### Key Components

Brief list of main files/classes involved (no code snippets).

### Key Dependencies

External services, jobs, events, queues this feature relies on.

## Related Resources

- **Linear:** [list ticket IDs and titles, or "None provided"]
- **Notion:** [list page titles and URLs, or "None provided"]
- **Related Docs:** [other docs in Documentation/features/ that relate]
```

### 4. Save the file

Write to `wize-back/Documentation/features/<kebab-case>.md`. Create the directory if it does not exist.

### 5. Offer Notion sync (optional)

After saving, offer once:

> "Doc saved to `<path>`. Want me to also create or update a Notion page for this?"

Only proceed if the user confirms. Use `notion-create-pages` for new pages or `notion-update-page` for existing ones.

### 6. Confirm

Return the workspace-relative path of the saved file and suggest a quick human review.
