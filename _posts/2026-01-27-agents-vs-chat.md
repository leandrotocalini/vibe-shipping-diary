---
layout: post
title: "Agents > Chat (and why my assistant now tracks my lunch)"
date: 2026-01-27 00:00:00 +0000
categories: [dev, ai]
---

I didn’t wake up one day and decide to become an “AI guy.”

I just wanted two boring things:

1) ship faster
2) lose a bit of weight without turning my life into a spreadsheet

Somewhere between those two goals, I ended up building an assistant that reads my calendar, nags me about peanuts, and makes me realize something important:

**Agents aren’t chat.** They’re outcomes.

## The moment it clicked

Chat is fun. You ask questions, it answers. Cool. Useful.

But chat is also… slippery. You finish the conversation and nothing in your life changed unless you changed it.

An agent is the opposite. The whole point is that something happens *outside the chat window*:

- a file gets created
- a script runs
- a reminder shows up at the right time
- a report lands in your inbox

When it works, it feels less like “talking to an AI” and more like hiring a tiny, tireless intern who never forgets.

## The dumbest example: tracking lunch

Here’s the actual workflow I’m using right now:

- I send a photo or a quick message: “lunch: chicken milanesa + salad + olive oil + a bit of cassava.”
- The assistant estimates calories (not perfect, but consistent)
- It saves it into a `raw.json` (source of truth)
- It regenerates a README summary
- It commits and pushes to GitHub

Is it overkill? Yes.

Is it weirdly motivating to see your day show up as data + a clean changelog? Also yes.

And the important part isn’t calories.

It’s the pattern:

**input → structure → automation → feedback loop**

That’s what “agents” really are.

## What I’m actually learning at 40

I’m not trying to become a full-time prompt poet.

I’m learning a new style of building:

- tiny scripts
- fast iterations
- let the model do the annoying parts
- keep the truth in a repo

I call it *vibe shipping*.

Not because vibes write code.

Because shipping is the only thing that makes the vibes real.

## What’s next

Next, I want the assistant to:

- generate weekly summaries (weight trend vs calories)
- keep a lightweight “voice guide” so my writing stays consistent
- help me ship small tools (and maybe the occasional dumb app) on a schedule

And yes — I’m also doing jiu jitsu.

Because nothing keeps you humble like getting folded in half by someone 20kg lighter.

If you’re also learning this stuff mid-life: welcome. We’re late, but we’re on time.
