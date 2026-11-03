---
name: "reprobates-lore"
description: "Read, search, add, or edit Reprobates canon in Nathan's Obsidian vault (characters, powers, world, plot, arcs, scenes), including turning brain-dumps into vault notes. Use whenever Nathan asks about Reprobates lore or wants something saved, updated, linked, or formalized, even without naming the vault."
---

# Reprobates vault

Canon lives in Nathan's Obsidian vault at `~/Desktop/Reprobates` on his Mac. The GitHub repo `NateGrenade/Reprobates` is only an automatic backup: Obsidian Git commits and pushes after edits. Canon order: vault > Google Drive drafts > chat memory.

## Access
- **Linked computer (default):** run commands with the device bash tool in `$HOME/mnt/Reprobates`. If that folder isn't mounted, request access to `~/Desktop/Reprobates` once. If you're running directly on the Mac, use `~/Desktop/Reprobates`.
- **No linked computer:** read the backup instead with `git clone -q --depth 1 https://github.com/NateGrenade/Reprobates /tmp/rep`, then run the same commands there. Tell Nathan the backup may lag slightly. This path is read-only: offer the text for him to paste, or wait until the computer is linked.
- Never run git in the vault, because Obsidian Git owns commits. `rm` is blocked; ask before deleting anything, and use `mv` to rename or move notes.

## Find
- Index of every note: `grep -rH -E '^(status|description):' --include='*.md' . | grep -v '^./Templates'`
- Full-text search: `grep -rli 'term' --include='*.md' .`
- Read matching notes in full. Follow a `[[link]]` one hop when it's load-bearing for the question.
- Answer from the note text. Keep names, numbers, and rules exactly as written. If nothing covers it, say so; don't fill gaps from general knowledge.

## Note conventions
- The filename is the title (Title Case, spaces OK). Names must be unique across the vault. Folders: `Characters/`, `World/`, `Power System/`, `Plot/` (+`Speculation/`), `Story/Arc N/` (+`Scenes/`, `Speculation/`). If nothing fits, propose a new folder.
- Front matter:
  ```
  ---
  status: provisional        # canon | provisional | proposal | speculation | rejected
  description: "one line"
  aliases: ["Alt Name"]      # optional, real alternate names only
  ---
  ```
- Sections are `##` headings. Link the first mention of each other note: `[[Elion]]`, `[[Supporting Cast#Chara|Chara]]`. Minor characters are sections in `Supporting Cast` until they earn their own note.
- There's no index to maintain: `Home.md` embeds `Lore Index.base`, which builds itself from front matter.
- Never promote a status (e.g. speculation → canon) without Nathan's say-so.

## Write
1. Read the target note(s) and grep for overlap. Flag any contradiction with existing notes before drafting around it. If the content extends an existing note, edit that note rather than duplicating it.
2. Show the proposed change in chat (full text for a new note, the changed passages for an edit) and wait for his OK. Mechanical fixes he asked for (links, typos, front matter) need no preview.
3. Write with a heredoc (`cat > "path" <<'EOF'`) or a short Python read-modify-write. Never retype an existing file from memory or from a truncated read.
4. Confirm in one line. The backup happens on its own.

Style: terse and matter-of-fact like the existing notes. Keep Nathan's invented terms and spellings exactly.

**Brain-dumps → notes** (Drive docs or pasted text; read the whole source): split into one note per coherent topic at the granularity of existing notes. Set the status to match the source's own confidence. Never invent facts to fill gaps. Present all the drafts plus what's left over, then write the approved ones per the steps above. For large sources, draft the clearest pieces first and ask before doing the rest.