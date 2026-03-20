# wize-doc

Cursor + Claude Code plugin to produce **business-focused feature documentation** from **code**, **Linear** (MCP), and **Notion** (MCP). Output is written to **`wize-back/Documentation/features/<kebab-case>.md`** in the Workwize repo (adjust if your backend root differs).

## MCP servers (required)

The plugin declares two **hosted** MCP servers (OAuth):

| Server | URL | Notes |
|--------|-----|--------|
| **linear** | `https://mcp.linear.app/mcp` | [Linear MCP docs](https://linear.app/docs/mcp) |
| **notion** | `https://mcp.notion.com/mcp` | [Notion MCP docs](https://developers.notion.com/docs/get-started-with-mcp) |

After enabling the plugin, complete authentication in your client (e.g. Cursor MCP panel; Claude Code `/mcp`).

**Claude Code** alternative install (per vendor docs):

```bash
claude mcp add --transport http linear-server https://mcp.linear.app/mcp
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

If you manage MCP only via the plugin, rely on [`.mcp.json`](.mcp.json) / [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json).

Clients that only support stdio can bridge with `npx -y mcp-remote <url>` (see Linear/Notion docs).

## Layout

- **Cursor:** [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json)
- **Claude Code:** [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) + [`.mcp.json`](.mcp.json)
- **Skill:** [`skills/document/SKILL.md`](skills/document/SKILL.md)
- **Command:** [`commands/document.md`](commands/document.md)

## Cursor install (local)

```bash
mkdir -p ~/.cursor/plugins/local
ln -snf /absolute/path/to/workwize/wize-doc ~/.cursor/plugins/local/wize-doc
```

Restart Cursor; authenticate **linear** and **notion** MCPs.

## Usage

- Run **`/document`** or ask to document a feature (e.g. *document auto-restock*).
- Workflow: scan existing `Documentation/features` → Linear/Notion prompts → code discovery → compose → **Write** markdown file.

## Compared to the old documentation MCP

Previously a single **documentation-mcp** bundled list/save/Linear/Notion. That integration is removed. Listing and saving docs use **workspace Glob/Write**; Linear and Notion use **official MCPs** only.
