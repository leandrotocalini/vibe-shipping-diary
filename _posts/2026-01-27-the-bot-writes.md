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

I like to think of this in two phases: **setup** (one-time) and the **posting loop** (repeatable).

### Phase 1 — Setup (one-time)

1) **WhatsApp.** I tell the bot: “Let’s build a blog.”
2) **Repo.** It creates the GitHub repo and gets GitHub Pages/Jekyll working.
3) **Iteration.** We tweak the look and the rules (theme, sections, what we do/don’t say).
4) **Voice calibration.** It reads my X/Twitter posts and builds a small voice guide.
5) **Done.** From that point on, it can write in my vibe without re-training every time.

### Phase 2 — The posting loop (repeatable)

1) **WhatsApp.** We iterate on the content here.
2) **Markdown draft.** The bot turns it into a post using the saved voice guide.
3) **Iteration.** We do a few passes: rewrite sections, tweak pacing, swap titles, clean up the diagram — until it feels right.
4) **Commit + push.** Git becomes the source of truth.
5) **Deploy.** GitHub Actions builds the site and GitHub Pages publishes it.
6) **Feedback.** I read it on my phone, adjust, repeat.
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


## The loop

```mermaid
flowchart TD
  A[WhatsApp\n(Hablo con el bot por WhatsApp)] --> B[Alfred (Clawdbot)]
  B --> C[Draft post in Markdown]
  C --> C2[Iterate\n(rewrite sections, adjust tone, polish)]
  C2 --> C
  C2 --> D[Commit + push to GitHub]
  D --> E[GitHub Actions builds Jekyll]
  E --> F[GitHub Pages deploy]
  F --> G[You read it on your phone]
  G --> A
```
