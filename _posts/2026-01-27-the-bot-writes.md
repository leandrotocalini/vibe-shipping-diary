---
layout: post
title: "I talk. My bot composes."
date: 2026-01-27 00:10:00 +0000
author: leandrotocalini
categories: [dev, ai]
tags: [writing, workflow, git]
---

![I talk, my bot composes](/assets/img/posts/the-bot-writes-hero.png)

This weekend, Clawdbot basically showed up all over my timeline like a new band everyone suddenly “discovered.” Same energy: clips, screenshots, hot takes, and people casually claiming it changed their workflow in 30 minutes.

So I installed it.

Not in the “I tested it for five minutes” way. In the “fine, take the wheel—let’s see if you can actually ship” way. And honestly? I’m still in that early stage of wonder where you keep finding one more thing it can do and your brain quietly files it under: *I should’ve done this sooner.*

That’s why this blog exists.

I wanted a place to keep a record of the stuff I’m building, learning, and stubbornly trying to improve—LLMs, vibe shipping, a bit of training, a bit of life logistics—without turning it into a polished personal brand project. More like a logbook. Something I can look back on in a month and say: “Yeah, that’s when it clicked.”

To keep it simple, I split the whole thing into two parts: **Blog setup** and **Post creation & updates**. One gets the machine running. The other is where the work actually happens.

## Blog setup

I came in with the most boring plan on earth: “Let’s make a GitHub Pages blog.” No platform drama, no new accounts, no fancy CMS. Just a repo and a URL.

Because the bot already had GitHub access, the first real decision wasn’t technical. It was identity. We threw names around until one felt like something I’d actually show to a friend: **Vibe Shipping Diary**. Once it had a name, it stopped being “a blog I might do” and became “a thing I’m building.”

Then we did the small-but-important tradeoff: Jekyll versus the modern stack. Astro and Next are great, but I wanted maximum shipping and minimum babysitting. So we picked **Jekyll + GitHub Pages** on purpose. Boring is a feature when you’re trying to build a habit.

The last step was voice. I told the bot: “Before you write, read me.” It pulled my recent posts via **bird**, extracted patterns, and built a tiny voice guide. Not to impersonate me, just to keep the writing consistent when I’m tired, distracted, or trying to do ten things at once.

After that, the system was ready. Repo is the source of truth. Deploy is automatic. The only remaining input is… me talking.

## Post creation & updates

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

This is the part where it stops being hype and becomes a habit.


