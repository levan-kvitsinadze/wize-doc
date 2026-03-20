---
name: code-explorer
description: Explore the Workwize codebase to find files relevant to a feature. Returns a structured analysis of business logic, endpoints, models, and key rules found. Used internally by the wize-doc agent.
---

# Code Explorer

Explore the Workwize codebase to find and analyze files relevant to a feature.

## Input

Feature name from the orchestrator (e.g. `auto-restock`, `budget approval`).

## Steps

### 1. Derive search tokens

From the feature name, extract 1–2 short tokens for glob matching:
- Lowercase, strip hyphens/spaces (e.g. `auto-restock` → try both `restock` and `autorestock`)
- Use the most specific token first; fall back to a broader one if results are sparse

### 2. Glob for matching files

Run these searches **in parallel** using the primary token:

- `**/*<token>*.php` — backend (Laravel)
- `**/*<token>*.ts` — TypeScript
- `**/*<token>*.tsx` — React components

Exclude: `vendor/`, `node_modules/`, `dist/`, `.next/`, `storage/`, `bootstrap/cache/`.

If results are sparse (< 5 files), also try the secondary token or broaden with related terms.

### 3. Broaden with structural patterns

In addition to token-based matches, scan these directories for contextually related files:

**Backend (PHP):**
- `wize-back/project/app/Http/Controllers/**/*<token>*`
- `wize-back/project/app/Services/**/*<token>*`
- `wize-back/project/app/Models/**/*<token>*`
- `wize-back/project/app/Jobs/**/*<token>*`

**Frontend (TS/TSX):**
- `wize-front/src/components/**/*<token>*`
- `wize-front/src/hooks/**/*<token>*`
- `wize-front/src/services/**/*<token>*`
- `wize-front/src/pages/**/*<token>*`

### 4. Present and confirm file selection

Show the combined file list to the user. Select the **10–15 most relevant** files based on:
- Direct name match (highest priority)
- Controllers and Services over generic utilities
- Models when business rules likely live there
- Frontend pages/components when user-facing workflows matter

Ask the user to confirm, remove irrelevant files, or add paths you may have missed.

### 5. Read all selected files

Read each confirmed file in full. For very large files (> 300 lines), read the most relevant sections first (class definition, key methods, validation rules).

### 6. Return structured analysis

Summarize findings for the doc-writer. Cover:

- **Key endpoints / entry points** — routes, controllers, API methods
- **Business logic** — conditions, branching, decision points
- **Validation rules** — what is checked and when
- **State transitions** — status changes, workflow steps
- **Data models** — key fields, relationships, constraints
- **Notable dependencies** — external services, jobs, events, queues

Keep the summary factual — extract what is in the code, don't invent behavior.
