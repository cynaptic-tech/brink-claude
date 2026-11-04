---
name: capture
description: Save what this conversation found or decided as a Brink note, or add it to one.
argument-hint: "[note title]"
disable-model-invocation: true
allowed-tools:
  - mcp__plugin_brink_brink__search_notes
  - mcp__plugin_brink_brink__get_note
  - mcp__plugin_brink_brink__list_notes
---

# Capture to Brink

Write up what this conversation has found, decided or produced, and save it in Brink. Follow the
`brink` skill's rules.

Title or note to add to: $ARGUMENTS

1. **Pick the note.** If a title was given, `search_notes` for it. If a note matches closely, add to
   it; otherwise make a new note with that title. With no title, make a new note with a short,
   specific title of your own (not "Notes" or "Summary").
2. **Write it** in Markdown, for the user reading it later without this conversation:
   - What it's about, in a sentence.
   - What was decided or found, and why.
   - What's left, as a short list.
   - Paths, commands, links and names spelled out exactly.
   - No chat narration ("I then…") and nothing the user didn't see in the conversation.
   - Link to related Brink notes with `[[Title]]` when you know they exist.
3. **Save it.** A new note: `create_note`. An existing one: `append_to_note`, starting with a
   heading that carries today's date, so the addition reads as its own section. Don't change what's
   already there.
4. **Tell the user** the note's title, whether it's new or added to, and that it isn't open in a
   tab (offer to open it with `open_tab`; don't open it unasked).
