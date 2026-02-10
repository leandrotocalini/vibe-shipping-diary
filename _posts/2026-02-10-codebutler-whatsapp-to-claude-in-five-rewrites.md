---
layout: post
title: "CodeButler: WhatsApp to Claude Code in Five Rewrites"
description: "How a simple idea — text my phone, have Claude Code do the work — took five architecture rewrites to get right."
date: 2026-02-10 13:00:00 -0300
categories: [dev, ai]
tags: [agents, claude-code, go, whatsapp, mcp]
image: /assets/img/posts/codebutler-hero.png
---

I wanted to text my phone and have Claude Code do the work. That's it. No container orchestration, no custom agent framework, no browser automation. Just WhatsApp in, Claude Code out.

I'd been using [OpenClaw](https://github.com/openclaw) for this — a full platform with container management, scheduled tasks, routing layers. Impressive engineering, but a massive attack surface. Things broke regularly. And the real killer was cost: OpenClaw calls the Claude API directly, so every token — even idle polling — goes through Anthropic's pricing. Claude Code runs through a Max subscription. Unlimited use, flat fee.

The gap between what I needed and what existed was enormous. So I built [**CodeButler**](https://github.com/leandrotocalini/codebutler) — a single Go binary that bridges WhatsApp and Claude Code. It took five architectural rewrites to get it right.

## The File Protocol That Lasted Ten Minutes

The first attempt was embarrassingly simple. Four JSON files in `/tmp/codebutler/`. Go writes `incoming.json`, Claude reads it. Claude writes `outgoing.json`, Go reads it. Both sides polling every second.

It worked for about ten minutes.

The problem was fundamental — both sides had to be running and polling simultaneously. If Claude wasn't watching for the file, messages vanished. The protocol was hand-documented in a markdown file, and relied on Claude faithfully following file-checking instructions. I was building a message bus out of filesystem polling.

## MCP Made WhatsApp a Native Tool

Claude Code supports **MCP** (Model Context Protocol) natively — you can expose tools that Claude discovers automatically and calls like any built-in capability. So instead of inventing a file protocol, WhatsApp became a tool.

The Go binary became an MCP server exposing five tools: `send_message`, `ask_question`, `get_pending`, `ack_message`, `get_status`. Configuration was a single `.mcp.json`. Claude Code started the server automatically. No daemons, no background processes. stdio in, stdio out.

But MCP is request-response. No server push. Claude still had to call `get_pending()` in a loop to check for messages. Sound familiar? It was the file protocol all over again, just through a fancier pipe.

Two things fixed the polling problem. **Long-polling** — `get_pending()` blocks for up to 30 seconds waiting for a message instead of returning immediately. And **CLAUDE.md as orchestrator** — a few paragraphs of instructions telling Claude to call `get_pending()` on startup, treat WhatsApp messages as prompts, and execute them. The config file replaced thousands of lines of orchestration code.

Then came **SQLite persistence** — every incoming message gets a UUID and is stored immediately. Unacknowledged messages reappear on the next call, even across server restarts. At-least-once delivery with zero infrastructure.

## The Inversion That Changed Everything

The MCP architecture had an elegant flaw: **the expensive thing was always running.**

Claude polls `get_pending()` in a loop. Each call costs tokens — even when idle. At roughly 60k tokens/hour just listening, the cost of an always-on WhatsApp bridge was absurd. The AI was the host, and Go was the tool. But Go costs nothing at idle, and the AI costs everything.

The insight: **the always-on process should be the cheap one.**

```
Before: Claude Code (always on, polling) → MCP → Go server → WhatsApp
After:  Go daemon (always on, idle)      → spawns claude -p → WhatsApp
```

The daemon polls SQLite every 2 seconds — costs nothing. When a WhatsApp message arrives, it spawns `claude -p "prompt" --output-format json`. Claude processes the task, the daemon sends the result back to WhatsApp, Claude exits. Zero cost until the next message.

I deleted the entire MCP package — 700 lines gone. No `.mcp.json`, no long-polling hack, no tool schemas. The irony: I spent weeks building an elegant MCP bridge, only to realize the bridge itself was the bottleneck.

## Per-Repo Isolation

Instead of one global server, each repo gets its own daemon. Each one has its own WhatsApp pairing, its own SQLite database, its own config — all living in `<repo>/.codebutler/`. Visible in WhatsApp's "Linked Devices" as `CodeButler:blog`, `CodeButler:api`, etc.

```bash
$ cd ~/Source/blog
$ codebutler
```

First run triggers an interactive setup: scan QR, pick a WhatsApp group, choose a bot prefix. After that, the daemon starts immediately. No shared state, no global config, no coordination between repos.

## Conversations That Don't Get Lost

The trickiest problem wasn't technical — it was conversational. When messages arrive while Claude is working, processing them in order breaks everything. Imagine sending "fix the auth bug," then "also update the README" while Claude works, then Claude asks "JWT or sessions?" and you reply "JWT." If the daemon processes messages FIFO, your follow-up gets treated as a new task and Claude's question goes unanswered.

The fix: **conversation-first priority.** After Claude responds, the daemon marks the timestamp. Messages arriving *after* that point are follow-ups — processed immediately with `--resume`. Messages that arrived *during* processing are queued and held until the conversation ends — 60 seconds of silence.

This means the active conversation always has priority. Queued messages often become outdated anyway — if Claude already fixed everything during the conversation, that stale "update the README" gets processed fresh, with full context of what changed.

## Voice Messages Close the Loop

Text worked perfectly. But WhatsApp isn't just text — people send voice messages. Especially from the couch, which is the whole point.

The first voice message arrived as `[Voice Message]`. Claude responded: *"I can't listen to voice messages."* Fair enough — Claude is a text model. But I can transcribe before Claude ever sees it.

WhatsApp voice messages arrive as encrypted OGG audio. whatsmeow provides the decryption keys. The pipeline: download, decrypt, send to **OpenAI Whisper**, store the transcribed text in SQLite. By the time the daemon's poll loop sees it, it's just text. Claude never knows the difference.

One subtle detail: the first attempt tagged transcriptions as `[voice message] text here`. Claude saw the brackets and assumed it was something it couldn't read. Switching to XML tags — `<transcribed-voice-message>` — fixed it. Claude reads XML as structured content, not as markers for missing data.

## What Stuck

Five packages. One binary. Zero idle cost.

The lesson that keeps repeating: **if your system has an expensive component and a cheap component, the cheap one should be always-on.** I went files → MCP → daemon, when daemon was the right answer from the start. Each step taught me something, but the simplest solution was the last one I tried.

The other lesson: **CLAUDE.md is more powerful than code.** I replaced an entire orchestration layer with a few paragraphs of instructions. When your runtime is an LLM, a markdown file *is* the framework.

CodeButler is running right now. This post was requested through it — a WhatsApp message that became a Claude Code session that wrote the words you're reading. The whole point was to make that loop invisible.

It finally is.
