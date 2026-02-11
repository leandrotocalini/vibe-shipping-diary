---
layout: post
title: "CodeButler: Typing From the Terminal and Sending Photos Over WhatsApp"
description: "Adding a local TUI input and bidirectional image support to CodeButler — including a raw mode rabbit hole."
date: 2026-02-11 10:00:00 -0300
categories: [dev, ai]
tags: [agents, claude-code, go, whatsapp, codebutler]
image: /assets/img/posts/codebutler-hero.png
---

CodeButler started as a WhatsApp-to-Claude bridge. Messages go in from the phone, Claude does the work, results come back. But I kept switching to WhatsApp just to type "run the tests" — even though I was sitting right at the terminal where the daemon was running.

So I added a text input row to the TUI.

## The Simple Version That Wasn't

The idea was straightforward: pin a `> ` prompt at the bottom of the terminal, keep the scrolling logs above it. Type a message, press Enter, it goes to Claude and echoes to WhatsApp so the group stays in sync.

```
Row 1-4:    Fixed header (group name, web port)
Row 5..N-1: Scroll region (logs, messages, Claude responses)
Row N:      Input prompt "> _"
```

I used ANSI scroll regions to keep the header fixed and the logs scrolling in the middle. All output went through a single `printLines()` method that managed cursor positioning — move to the bottom of the scroll region, print, redraw the prompt. A mutex protected everything so multiple goroutines wouldn't interleave escape sequences.

It looked correct. It didn't work at all.

## Two Bugs Conspiring Together

Typing "Test" and pressing Enter did nothing visible. The text appeared in the wrong part of the screen, never reached WhatsApp, and Claude never saw it.

**Bug one: a rogue print.** Deep inside the WhatsApp client, `Connect()` had a `fmt.Fprintln(os.Stderr, "Connected to WhatsApp")` — a leftover from early development. This single line bypassed the Logger's scroll region and wrote directly to the prompt row. The trailing newline scrolled the entire screen by one line, shifting everything out of alignment. Every subsequent cursor operation wrote to positions that no longer matched reality.

**Bug two: cooked mode can't work here.** The standard terminal line discipline echoes typed characters at whatever position the cursor happens to be. If a log line arrives while you're typing, the cursor jumps to the scroll region, and your next keystroke echoes *there* instead of the prompt. Pressing Enter at the last row generates a newline that scrolls everything — header, scroll region, all of it. Each Enter press degraded the layout further.

## Raw Mode Fixed Everything

The fix was `term.MakeRaw`. Raw mode disables terminal echo entirely — no character appears unless I explicitly draw it. Each keystroke goes through the Logger's cursor management, protected by the same mutex that handles log output. The prompt, the typed text, and the log lines never fight over cursor position.

The input handler manages backspace (with proper UTF-8 multi-byte awareness), Ctrl+U (clear line), Ctrl+W (delete word), and Ctrl+C/D (shutdown). It silently swallows escape sequences from arrow keys. It's not readline — no cursor movement within the line, no history — but it's solid enough for sending prompts to Claude.

One subtlety: raw mode disables signal generation, so Ctrl+C no longer produces SIGINT automatically. The input handler catches byte `0x03`, restores the terminal state, and sends SIGINT to itself. This preserves the existing signal-based shutdown flow without special-casing raw mode in the daemon.

## Teaching Claude to Send Photos

Until now, communication between Claude and WhatsApp was text-only in the outbound direction. Claude could *receive* images (via `<attached-image>`), but if it wanted to share something visual — a screenshot, a chart, a test result — it had no way to do it.

This matters more than it sounds. Ask Claude to run your test suite, and it finds failures with visual diffs. Or it generates a diagram to explain an architecture decision. Or it takes a screenshot of a UI it just modified. Without image support, it has to *describe* what it sees instead of just showing you.

The solution is a symmetric counterpart to the existing `<attached-image>` tag. Claude's system prompt now includes:

> To send the user an image file, wrap the absolute path in your response:
> `<send-image path="/absolute/path/to/file.png">optional caption</send-image>`

When the daemon receives Claude's response, it scans for `<send-image>` tags with a regex. For each match, it reads the file, sends it to WhatsApp as an image message with the caption, and strips the tag from the text. Whatever text remains gets sent as a normal message. If the response was *only* an image tag, no text message is sent at all — just the image.

## Why a Tag Instead of a Tool

Claude already has filesystem access — it could write files. But it has no "send to WhatsApp" tool. The tag approach is lighter: no MCP extension, no tool registration, no extra round trips. Claude includes the tag in its normal text response, and the daemon handles the rest on the way out. It's post-processing, not interaction.

The original `SendImage` in the WhatsApp client hardcoded `image/png` as the mimetype. Since Claude might reference JPEGs, PNGs, or anything else, it now uses `http.DetectContentType` to sniff the first bytes and pick the right mimetype automatically. If detection fails, it falls back to JPEG.

## The Pattern

Both features follow the same principle that keeps showing up in CodeButler: **the daemon handles the plumbing, Claude handles the thinking.** The TUI is just another message source — same as WhatsApp, same SQLite path, same poll loop. The image tag is just another post-processing step on Claude's text output. No new protocols, no new abstractions. The system grows by adding thin layers to the existing flow.
