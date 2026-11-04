---
name: tasks
description: Turn this conversation's plan or to-dos into a Brink task list, or bring one up to date.
argument-hint: "[task list title]"
disable-model-invocation: true
allowed-tools:
  - mcp__plugin_brink_brink__search_notes
  - mcp__plugin_brink_brink__get_note
  - mcp__plugin_brink_brink__list_notes
---

# Brink task list

Keep a Brink task list in step with the work in this conversation. Follow the `brink` skill's
rules.

Task list: $ARGUMENTS

1. **Find the list.** With a title, `search_notes` for it and use a task list that matches. With
   no title, look for a task list this conversation already used; otherwise make one named after
   the work.
2. **Gather the tasks** from the conversation: the plan, the steps left, the follow-ups. One line
   each, starting with a verb, specific enough to act on later. Sub-steps nest under their task.
3. **New list:** `create_task_list` with the tasks, using `depth` for nesting, and `done: true` for
   ones already finished here.
4. **Existing list:** `get_note` first, then:
   - `add_task` only for tasks that aren't there in some wording (match by meaning, not exact
     text). Put a sub-task under its parent with `parent_id`.
   - `update_task` with `done: true` for tasks this conversation finished.
   - Leave everything else alone: don't reword, reorder or uncheck the user's tasks.
5. **Tell the user** what you added and checked off, in a short list, and the list's title. If
   nothing changed, show what's still open on the list.
