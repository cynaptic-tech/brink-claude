---
name: setup
description: Connect Claude Code to Brink, or find out why the connection doesn't work.
disable-model-invocation: true
---

# Set up Brink

Walk the user through connecting Claude Code to Brink, one step at a time, stopping at the first
step that isn't done. The user does each step in Brink and in Claude Code; you check.

**Never ask for the token in this chat, and never print it.** It goes only into the plugin's
options, which Claude Code keeps in its secure storage.

## 1. Find Brink

Run:

```bash
"${CLAUDE_PLUGIN_ROOT}/scripts/brink-mcp" --check
```

The plugin's Brink app option is: `${user_config.app}`. If that's a path, add `--app` and the path
to the command; if it's empty or a `${…}` placeholder, the option isn't set: leave `--app` out.

It prints which copy of Brink it found, its version, whether its MCP server is signed by Cynaptic
and unmodified, and whether Brink is running. It never starts the server, and it doesn't see the
token (the Bash tool's shell doesn't have it), so it can't check that: step 5 does. If it found an
MCP server, the version is new enough, whatever number it shows.

- **Not installed, or no MCP server:** AI agent access needs Brink 0.3.0 or later: the user
  updates Brink from the App Store.
- **Not signed by Cynaptic, or changed:** stop. Don't work around it. That copy of Brink isn't
  genuine, or is damaged; the user should reinstall Brink.
- **Spotlight can't find it:** set the **Brink app** option to where Brink.app is.
- **Not running:** ask the user to open it.

## 2. Turn on AI agent access in Brink

AI agent access is part of Brink Pro. In Brink, it's in Settings › AI Agents.

## 3. Add a connection

In the same place, the user adds a connection for Claude Code and picks its access:

- **Read only:** Claude can read notes, task lists and the layout, and change nothing.
- **Read and write:** Claude can also edit notes and task lists, and arrange workspaces and tabs.
  Claude Code still asks before each change, unless the user allowed the tool.

Brink shows the token (`brk_…`) once: the user chooses **Copy Token**.

## 4. Give the token to the plugin

The user runs `/plugin`, opens Installed › brink › Configure, and pastes the token into
**Connection token**. Then `/mcp` › plugin:brink:brink › Reconnect (or `/reload-plugins`).

If the user had added Brink by hand before (`claude mcp add brink …`), suggest
`claude mcp remove <name>`, so Brink's tools aren't listed twice.

## 5. Check

Call `get_layout` (read-only). If it answers, tell the user how many workspaces and tabs Brink has
and that the connection works, and which access it has: with a Read only connection, Brink's
writing tools leave the tool list after the first call. That's expected, not a disconnection. If it fails, Brink's error says why: follow the table in the
`brink` skill, then check again.
