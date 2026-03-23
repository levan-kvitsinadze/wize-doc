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

If you manage MCP only via the plugin, rely on [`.mcp.json`](.mcp.json) (referenced from [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json)).

Clients that only support stdio can bridge with `npx -y mcp-remote <url>` (see Linear/Notion docs).

## Layout

- **Cursor:** [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json)
- **Claude Code:** [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) + [`.mcp.json`](.mcp.json)
- **Skills:** [`skills/code-explorer/SKILL.md`](skills/code-explorer/SKILL.md), [`skills/doc-writer/SKILL.md`](skills/doc-writer/SKILL.md)
- **Agent:** [`agents/wize-doc.md`](agents/wize-doc.md)
- **Command:** [`commands/document.md`](commands/document.md)

## Claude Code install (local)

Install or symlink the plugin under your project’s `.claude/plugins/` (or enable it in `.claude/settings.json`) per [Claude Code plugins](https://docs.anthropic.com/en/docs/claude-code/plugins).

**If the plugin does not show up**, Claude Code sometimes fails to detect local plugins. Run the CLI with an explicit plugin directory (from the monorepo root, adjust the path if yours differs):

```bash
claude --plugin-dir ./wize-doc/
```

That loads **`wize-doc`** directly from this folder (the one that contains [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json)).

**Coming soon:** a **`marketplace.json`** for this plugin (or the monorepo) will be added so Claude Code can discover it reliably via a marketplace—until then, use **`--plugin-dir`** when auto-detection fails.

## Cursor install (local)

Cursor loads local plugins from a **real directory** under `~/.cursor/plugins/local/`. A **symlink to your repo often does not work** — copy the plugin tree instead.

```bash
mkdir -p ~/.cursor/plugins/local
# Replace with your clone path. --exclude '.git' keeps the install small; omit the exclude if you prefer a full copy.
rsync -a --delete --exclude '.git' /absolute/path/to/workwize/wize-doc/ ~/.cursor/plugins/local/wize-doc/
```

After you change files in the repo, run the same `rsync` again to refresh the installed copy.

**Enable the plugin in Cursor** so commands load. In the project’s `.cursor/settings.json` (or your user settings), add:

```json
{
  "plugins": {
    "wize-doc": { "enabled": true }
  }
}
```

Restart Cursor; authenticate **linear** and **notion** MCPs.

If **`/document`** does not appear, try **`/wize-doc:document`** (namespaced form).

### Plugin does not show up or commands are missing

1. **Open the Workwize monorepo root** as the Cursor folder so `workwize/.cursor/settings.json` (with `"wize-doc": { "enabled": true }`) applies. Opening only `wize-back/` or `wize-front/` skips that file unless you copy the same `plugins` block into **User** settings or that subfolder’s `.vscode/settings.json` / `.cursor/settings.json`.
2. **Reload the window** after changing the plugin or settings (`Developer: Reload Window`).
3. Confirm a **real** install: `test -f ~/.cursor/plugins/local/wize-doc/.cursor-plugin/plugin.json && echo OK` (not a broken symlink).

## Usage

- Run **`/document`** or ask to document a feature (e.g. *document auto-restock*).
- Workflow: scan existing `Documentation/features` → Linear/Notion prompts → code discovery → compose → **Write** markdown file.

## Compared to the old documentation MCP

Previously a single **documentation-mcp** bundled list/save/Linear/Notion. That integration is removed. Listing and saving docs use **workspace Glob/Write**; Linear and Notion use **official MCPs** only.
