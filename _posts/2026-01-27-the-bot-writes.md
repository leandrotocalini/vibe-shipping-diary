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

I like to think of this in two phases: setup (one-time) and the posting loop (repeatable).

### Phase 1 — Setup (one-time)

I came in with the most boring plan on earth: “Let’s make a GitHub Pages blog.” No platform drama, no new accounts, no fancy CMS. Just a repo and a URL.

Because the bot already had GitHub access, the first real decision wasn’t technical. It was identity. We threw names around until one felt like something I’d actually show to a friend: **Vibe Shipping Diary**. Once it had a name, it stopped being “a blog I might do” and became “a thing I’m building.”

Then we did the small-but-important tradeoff: Jekyll versus the modern stack. Astro and Next are great, but I wanted maximum shipping and minimum babysitting. So we picked **Jekyll + GitHub Pages** on purpose. Boring is a feature when you’re trying to build a habit.

The last step was voice. I told the bot: “Before you write, read me.” It pulled my recent posts via **bird**, extracted patterns, and built a tiny voice guide. Not to impersonate me, just to keep the writing consistent when I’m tired, distracted, or trying to do ten things at once.

After that, the system was ready. Repo is the source of truth. Deploy is automatic. The only remaining input is… me talking.

### Phase 2 — The posting loop (repeatable)

The loop is intentionally simple. I talk to the bot on WhatsApp, usually in messy half-sentences, and we shape it into something readable. The iteration part is the point: we rewrite paragraphs, swap titles, cut filler, and keep going until it feels like a post I’d stand behind.

Sometimes that means creating a brand-new post. Sometimes it means revisiting one that’s already live. I actually like that: publishing isn’t a final state, it’s just a snapshot. If a section is unclear, or the flow doesn’t land, we patch it and ship again. Git keeps the history honest.

Once the draft is good, everything else is just a normal dev move: commit, push, and let the pipeline do the boring part. GitHub Actions builds the site, GitHub Pages deploys it, and I read it on my phone like any other reader. Then, inevitably, I find one sentence I want to rewrite — and the loop starts again.

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


