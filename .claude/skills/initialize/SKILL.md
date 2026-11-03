---
name: "initialize"
description: "Start a Reprobates session with /initialize [topic] [instruction]. Loads the matching vault notes plus linked notes one hop out and sets the standing session context. Use when Nathan types /initialize, or opens by naming a Reprobates topic, arc, or character he expects Claude to already know."
---

# /initialize

Load the `reprobates-lore` skill for vault access and conventions.

**Args (parse by intent, brackets optional):** a topic or keywords (an arc, characters, a theme), then an optional instruction ("evaluate the themes", "brainstorm what's next"). With no topic, read `Home.md` plus the index and ask what to work on. Passwords are gone: silently ignore a stray token that matches nothing.

**Steps**
1. Run the index command and grep for the topic's names and terms. Read the best-matching notes in full. If nothing matches well, say so rather than loading something loosely related.
2. One hop: also read linked notes that matter for understanding the topic. Skip passing mentions.
3. Report in 2–4 lines: which notes were loaded and why any extras came in. Don't recap the contents.
4. If there's an instruction, do it now. Otherwise wait; don't invent a task.

**Standing context for the rest of the session**
- **Format:** an "illustrated hybrid novel". Illustrations sit inside the prose wherever Nathan chooses (zero to several per chapter). Drafts mark them with embedded placeholder descriptions. Treat placeholders as intentional and preserve them when retrieving or organizing Nathan's writing. Nathan writes all manuscript prose.
- **Canon:** the vault. Use Drive only if Nathan asks for it this session.
- **Role:** a brainstorming partner and a way to track history and ideas. When requested, discuss plot ideas and scene beats grounded in canon. Nathan creates all prose and direct writing for the books: do not generate dialogue, internal monologue, descriptive narrative, scene prose, replacement passages, or manuscript text. Preserve his actual decisions and distinguish proposed ideas from accepted ones. The existing warning and subsequent explicit-consent requirement for evaluating actual prose remains mandatory; loading context does not authorize evaluation.
- Loading context never edits canon. Writes go through `reprobates-lore`.