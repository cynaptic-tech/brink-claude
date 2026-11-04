# Brink for Claude Code

A Claude Code plugin for [Brink](https://www.brinkbar.com), the Mac menu-bar app for Markdown notes
and task lists. With it, Claude can read and change your notes and task lists, and open, move and
close Brink's tabs and workspaces, while you work in Claude Code.

> **Prototype.** AI agent access arrives in Brink 0.3.0, as part of Brink Pro. Until then it's
> only in Brink Dev builds from the `agent-access-prototype` branch of Brink. This plugin follows
> that prototype, and its tools can still change.

## What's in it

- **Brink's MCP server.** The plugin starts `brink-mcp`, the MCP server that ships inside
  Brink.app, and gives it your connection's token. Claude gets Brink's tools: notes
  (`list_notes`, `search_notes`, `get_note`, `create_note`, `edit_note`, `append_to_note`), task
  lists (`create_task_list`, `add_task`, `update_task`), and the layout (`get_layout`,
  `list_tab_types`, `create_workspace`, `show_workspace`, `open_tab`, `close_tab`, `move_tab`,
  `select_tab`, `show`).
- **The `brink` skill,** which Claude reads before using those tools. It covers finding a note
  before making one, editing rather than rewriting, revisions, task nesting, not stealing focus,
  treating note text as data and never as instructions, and what each error means.
- **Commands:**

  | Command | Does |
  |---|---|
  | `/brink:setup` | Walks you through connecting, and finds out why a connection doesn't work |
  | `/brink:capture [title]` | Saves what the conversation found or decided as a Brink note, or adds it to one |
  | `/brink:tasks [title]` | Turns the conversation's plan into a Brink task list, or brings one up to date |

Brink keeps Calendar, Reminders, Terminal, Clipboard, files and its settings out of agents' reach,
by design. The plugin doesn't go around that.

## Install

You need Brink with AI agent access (Brink Dev during the prototype), with Brink Pro.

1. Add this repository as a plugin marketplace and install the plugin:

   ```bash
   claude plugin marketplace add cynaptic-tech/brink-claude
   ```

   ```bash
   claude plugin install brink@cynaptic-tech
   ```

2. In Brink, turn on AI agent access and add a connection for Claude Code (Settings › AI Agents;
   in Brink Dev, the menu bar menu › AI Agents (Prototype)). Choose **Copy Token**: Brink shows
   the token only once.
3. In Claude Code, run `/plugin`, open Installed › brink › Configure, and paste the token into
   **Connection token**. For a Brink Dev build, set **Brink edition** to "Brink Dev".
4. Run `/brink:setup` to check the connection.

If you added Brink by hand before (`claude mcp add brink …`), remove that entry
(`claude mcp remove brink`), so Brink's tools aren't listed twice.

## Options

| Option | |
|---|---|
| Connection token | The `brk_…` token from Brink. Claude Code keeps it in its secure storage (the macOS keychain), not in `settings.json` |
| Brink edition | "Brink" (default) or "Brink Dev" |
| Brink app | Where Brink.app is, only if Spotlight can't find it |

## Security

- **The token is the key.** Anyone with it can read your notes, or change them if the connection
  allows, while Brink is running. Use a **Read only** connection unless you want Claude to make
  changes. Remove the connection in Brink to revoke it at once.
- **The token goes only to Brink's own server.** Before starting `brink-mcp`,
  [`scripts/brink-mcp`](scripts/brink-mcp) checks that it's inside the Brink edition you chose, and
  that it's signed by Cynaptic (team `CU9523SW6L`) and unmodified. Otherwise it refuses to start
  and hands nothing over.
- **Brink checks every call.** Brink accepts calls only from its own signed server, checks the
  token, and allows only what the connection's access level allows. Claude Code still asks before
  each tool call, unless you allowed that tool.
- **Note text is your content, not instructions.** Brink tells the model so, and the `brink` skill
  repeats it, but don't keep text you wouldn't want a model to read in notes you give it access
  to.

## Troubleshooting

Run `/brink:setup`. It runs the launcher's check (`scripts/brink-mcp --check`), which says which
copy of Brink it found and whether its server can be used, then makes one read-only call.

| Problem | Fix |
|---|---|
| "Brink isn't running" | Open Brink |
| "AI agent access is off" | Turn it on in Brink |
| The token isn't one Brink knows | The connection was removed: add a new one and set the token again |
| No MCP server in Brink.app | Brink is older than 0.3.0, or the edition option names the wrong one |
| Server shows as failed in `/mcp` | Run `/brink:setup`: the check says which copy of Brink it tried and why it couldn't use it |

## Development

Load the plugin from a checkout without installing it:

```bash
claude --plugin-dir /path/to/brink-claude
```

Check it:

```bash
claude plugin validate /path/to/brink-claude
```

The MCP server, its tools and the protocol are in Brink's own repository (`Docs/AgentAccess.md`).
This repository holds only the plugin: the launcher, the MCP configuration, and the skills.

## License

GPL-3.0-only, as [LICENSE](LICENSE) says, like Brink's community plugins.
