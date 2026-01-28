---
layout: post
title: "I talk. My bot composes."
date: 2026-01-27 00:10:00 +0000
author: leandrotocalini
categories: [dev, ai]
tags: [writing, workflow, git]
---

If you’re reading this, it means two things happened:

1) I gave a bot access to my GitHub.
2) It didn’t immediately delete everything.

Small wins.

More specifically: I created a private GitHub organization for personal work, added **Clawdbot** to it, and told it to do something that sounds simple but is deceptively hard:

> “Read my tweets and write like me.”

Not *exactly* like me (that would be a crime against punctuation), but close enough that the jokes land.

This is the part that matters:

**the repo is the source of truth.**

Not a note app. Not a magical chat transcript. Not a pile of screenshots.

A repo.

That’s how this blog exists.

## Linking the first post to the process

In the first post, I wrote about how the “agents > chat” idea clicked for me.

- An agent doesn’t just answer.
- It *produces outcomes*.

This is the same thing — applied to writing.

I don’t want a model that can write a nice paragraph once.

I want a workflow that reliably ships posts.

## The workflow (current version)

Here’s what we do now:

1) **Talk here.**
   - We outline the post in a few messages.
   - We keep the tone: humor, but no cheap insults. No obvious politics.

2) **Voice calibration.**
   - Clawdbot reads my recent X/Twitter posts.
   - It builds a small “voice guide” (patterns, pacing, phrases).
   - The goal isn’t mimicry — it’s consistency.

3) **Write a draft in Markdown.**
   - Title + frontmatter.
   - Short, tight sections.

4) **Commit & push to GitHub.**
   - The repo becomes the canonical version.
   - Version history is a feature, not a bug.

5) **GitHub Pages deploys automatically.**
   - No “publish” button.
   - Shipping is just pushing.

That’s it.

Low ceremony. High leverage.

## Why this feels different

The “AI blog post” thing is everywhere.

What’s less common is building a system that:

- keeps your writing voice consistent
- doesn’t rely on you remembering what you decided last week
- makes publishing feel like a normal dev action

Basically: *treat writing like shipping.*

If you want the starting point, read the first post:

- [Agents > Chat (and why my assistant now tracks my lunch)]({% post_url 2026-01-27-agents-vs-chat %})

Then come back here.

This is the part where it stops being hype and becomes a habit.
