---
layout: post
title: "CodeButler: Sessions That Don't Explode"
description: "How CodeButler manages Claude sessions — continuation markers, input waiting, and a compact watchdog that keeps context bounded."
date: 2026-02-11 11:00:00 -0300
categories: [dev, ai]
tags: [agents, claude-code, go, whatsapp, codebutler]
image: /assets/img/posts/codebutler-sessions-hero.jpg
---

The first version of CodeButler spawned `claude -p` with `--max-turns 10` and waited for it to finish. Simple, but the user saw nothing until Claude was done — which could take minutes on a complex task. And sessions grew forever, making each `--resume` slower as the context accumulated.

I needed three things: visible progress, patience for user input, and bounded session growth. Each one got its own mechanism, and they all work through the same trick — markers in Claude's output.

## `[CONTINUING]`: Progress You Can Actually See

The problem with `--max-turns 10` and `--output-format json` is that you get a single JSON blob at the end. No partial output, no indication of progress. For a task that takes 20 turns, the user stares at silence for minutes.

The fix: lower `maxTurns` to 5 and add an instruction to the system prompt:

> *"If you hit the turn limit and still have more work to do, summarize what you accomplished so far and what you will do next, then end with [CONTINUING]"*

The daemon loop now checks every response:
1. Run `claude -p` with `--max-turns 5`
2. Response contains `[CONTINUING]`? Strip the marker, send the partial response to WhatsApp, auto-resume with "Continue from where you left off."
3. No marker? Done — exit the loop.

The user sees progress every ~5 turns instead of waiting for the whole thing. There's a token trade-off — each `--resume` reloads the entire session history. A 20-turn task generates 4 reloads instead of 1. But seeing progress is worth the overhead. If it matters, bump `maxTurns` in `config.json`.

## `[NEED_USER_INPUT]`: Waiting Without a Timer

When Claude proposes a plan and asks "Reply *yes* to implement," the user might take five minutes to respond. Or five hours — maybe they went to make dinner, maybe they're thinking about it overnight. The compact watchdog (described below) would normally clean the session after 10 minutes of silence.

Same pattern, different marker:

> *"When you need user confirmation or any input before you can proceed, end with [NEED_USER_INPUT]"*

The daemon tracks this with a `convWaitingInput` flag. When set, the compact watchdog skips this session entirely — it won't compact something that's actively waiting for a human.

The elegant part: there's no logic to "clear" the flag. Claude puts the marker when it needs input, and doesn't when it doesn't. The flag simply reflects Claude's latest state. When the user replies and Claude proceeds, the next response either has the marker again (still needs input) or doesn't (ready to work). The daemon just follows Claude's lead.

## The Compact Watchdog

Sessions grew indefinitely. Each `--resume` loaded the entire conversation history — every message, every response, every code block. After a productive afternoon, resuming a session meant re-reading thousands of tokens of context that was mostly irrelevant.

Compacting on `endConversation()` (60 seconds of no reply) was too aggressive. Sometimes I'd come back in 2 minutes with a follow-up and the session was already gone.

The compact watchdog is an independent goroutine with a simple rule: **10 minutes of total silence.**

1. It waits for an activity signal (message in, Claude response, TUI input)
2. After the last activity, starts a 10-minute timer
3. Any activity resets the timer
4. When 10 minutes pass with zero activity, it checks three conditions: not busy, no active conversation, not waiting for input
5. If all clear, it resumes Claude with: *"Summarize this conversation: key decisions, what was done, current state, pending items"*
6. Saves the summary in SQLite's `context_summary` column
7. Clears the `session_id` — Claude's session is discarded

The next message after compaction starts a fresh Claude session. But since there's no `session_id` to `--resume`, the daemon looks up the `context_summary` and prepends it:

```
<previous-context>
{compacted summary}
</previous-context>

[2026-02-11T...] Leandro: new message here
```

Claude starts fresh but with context from before. Bounded growth — the summary is a few hundred tokens regardless of how long the original session was.

## The Escape Hatch

Sometimes previous context is irrelevant or actively confuses Claude. The `/cleanSession` command deletes the entire database row — session ID and summary. The next message starts 100% clean, as if CodeButler had never talked to Claude before in this repo.

The schema tells the story:

```sql
sessions (
  chat_jid        TEXT PRIMARY KEY,
  session_id      TEXT DEFAULT '',    -- Claude session for --resume
  context_summary TEXT DEFAULT '',    -- compacted summary
  updated_at      TEXT
)
```

`ResetSession()` clears only `session_id` — session gone, summary preserved. `ClearSession()` deletes the row entirely — blank slate.

## Markers Over Mechanics

All three features — continuation, input waiting, compaction — work through the same pattern: **a marker in Claude's text output that the daemon interprets.** No new tools, no MCP extensions, no protocol changes. Claude writes `[CONTINUING]` or `[NEED_USER_INPUT]` as part of its natural response, and the daemon acts on it.

This keeps the system prompt as the single source of truth for Claude's behavior. The Go code doesn't need to understand *what* Claude is doing — it just needs to recognize a few markers and route accordingly. The intelligence stays in the LLM, the plumbing stays in Go.
