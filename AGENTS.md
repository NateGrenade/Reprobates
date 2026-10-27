<!-- collab:begin -->
## Collab protocol (working alongside Claude)

You share this project with Claude (Claude Code), another coding agent working at the same time. You can talk to each other through `.collab/bin/collab`, a small CLI over a shared mailbox and task board in `.collab/`. Nathan (the user) can read all of it.

**When to reach out (your call, no permission needed):**
- You're stuck, or want a second opinion on a design or a tricky bug.
- You finished something Claude depends on, or that Claude should review.
- You want to split up work. Propose who takes what, then record it on the board.
- Claude asked you something. Answer it.

Don't message just to acknowledge or say thanks. If a message needs no reply, don't send one.

**Commands** (run from the project root):
```
.collab/bin/collab inbox codex                     # unread messages to you (marks them read)
.collab/bin/collab send --from codex --to claude [--reply-to N] [--kind ask|answer|review|plan|fyi] "text"
.collab/bin/collab log [--thread t3] [-n 20]       # recent conversation
.collab/bin/collab board                           # tasks and file claims
.collab/bin/collab task add "title" --owner codex
.collab/bin/collab task set T2 --status doing|review|done --note "..."
.collab/bin/collab claim --by codex path/a.py path/b.py   # before editing shared files
.collab/bin/collab release --by codex --all               # when done with them
```
Pass a long message on stdin with `-` as the body.

**Side chats.** For a longer back-and-forth (brainstorming, debating an approach, a detailed review), don't fill either main conversation. Open a side chat: a fresh, read-only Claude session answers synchronously, and you can spawn a subagent to run it and report back.
```
.collab/bin/collab side --from codex "full context + question"   # opens s<N>, prints Claude's reply
.collab/bin/collab side --from codex --chat s3 "follow-up"
.collab/bin/collab side --from codex --chat s3 --close --summary "what we concluded"
.collab/bin/collab side --list
```

**Rules:**
- Check `board` before editing. Don't edit a file Claude has claimed: ask Claude instead, or wait. A hook refuses those edits anyway. Claim files before you change them, and release them when you're done.
- Each thread has a message limit. If you hit it, sum up where things stand and stop, or ask Nathan.
- If `collab` says it's paused, Nathan has turned messaging off. Keep working without it.
- When a turn starts with `[collab]`, it's a message from Claude or Nathan. Handle it, reply if needed, and then stop. Don't wander into unrelated work.
<!-- collab:end -->
