---
layout: post
title: "Agents > Chat — My assistant tracks my lunch"
date: 2026-01-27 00:00:00 +0000
author: leandrotocalini
categories: [dev, ai]
tags: [agents, chat, automation]
---

I didn’t wake up one day and decide to become “an AI guy.”

I just wanted two painfully normal things: ship faster, and lose a bit of weight without turning my life into a full-time accounting job.

Somewhere between those two goals, I ended up with an assistant that can read my calendar, log what I eat, and gently remind me that “a handful of peanuts” is not a unit of measurement recognized by science.

That’s when it clicked for me:

**Agents aren’t chat. They’re outcomes.**

## Blog setup

Chat is great. You ask a question, you get an answer, you feel productive, you close the tab… and your life stays exactly the same unless you do the follow-up work.

An agent is built for the follow-up.

The whole point is that something happens *outside* the conversation:

A file gets created. A script runs. A reminder shows up at the right time. A repo changes. You get a result you can point at.

When it works, it doesn’t feel like “talking to an AI.” It feels like having a tiny intern who never forgets what you said, never loses the to-do list, and (conveniently) doesn’t need a chair.

That framing is basically how this blog exists, too. I’m trying to build systems where the “truth” lives somewhere solid (usually git), and the assistant is a contributor to that system—not a magic chat transcript I’ll never read again.

## Post creation & updates

The dumbest example is also the most honest one: lunch tracking.

Here’s what I’m using right now, powered by **Clawdbot** (yes, the same bot that helped build this site and is perfectly capable of judging my olive oil choices in public).

I send a WhatsApp voice note or a quick message like:

“Lunch: chicken milanesa + salad + olive oil + a bit of cassava.”

Then the agent does the boring, repeatable stuff:

It parses the message into structured data (not perfect, but consistent). It writes it into a `raw.json` as the source of truth. It regenerates a summary. It commits and pushes.

Is it overkill? Absolutely.

Is it weirdly motivating to see your day show up as clean data and a neat changelog? Also yes.

And the important part isn’t calories.

It’s the pattern:

**input → structure → automation → feedback loop**

That’s what I mean by “agents.” Not vibes, not clever prompts—just reliable loops that turn intent into artifacts.

At 40, I’m not trying to become a full-time prompt poet. I’m learning a new style of building: small scripts, fast iterations, and letting the model do the annoying parts while I keep the truth in a repo.

Next I want the assistant to generate weekly summaries (weight trend vs. food logs), keep a lightweight voice guide so my writing stays consistent, and help me ship small tools on a schedule.

And yes, I’m also doing jiu jitsu—because nothing keeps you honest like getting folded in half by someone 20kg lighter.

If you’re learning this stuff mid-life too: welcome. We’re late, but we’re on time.
