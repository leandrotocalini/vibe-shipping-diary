---
layout: post
title: "Sentry + MCP + Claude: Turning Error Triage Into Operational Memory"
date: 2026-03-12 00:30:00 -0300
categories: [ai, development]
image: /assets/img/posts/sentry-mcp-claude-hero.png
---

I didn't want Claude to be "smart about Sentry" in the abstract. I wanted it to be smart about **my** system.

That's a different problem.

Generic AI help with error monitoring usually looks like this: you paste an issue, it gives you a plausible explanation, and then everyone moves on. Sometimes it's useful. Sometimes it's just expensive autocomplete over stack traces.

What I wanted instead was a workflow.

A repeatable way for Claude to look at Sentry, query the system through MCP, compare what it found against the roadmap, and tell me whether an error was:

- a real new problem
- noise
- a duplicate of work already planned
- or evidence that a recent improvement actually changed system behavior

That turns "AI assistant" into something closer to **operational memory**.

## The Real Problem

The system itself is a multi-org operations platform for sawmills. Workers report through WhatsApp using text, voice notes, and photos. AI turns those messy human messages into structured operational events.

That means errors are not isolated little backend exceptions. They sit inside a pipeline with context:

- conversations
- classification
- learning/digest state
- anti-patterns
- roadmap milestones
- recent fixes
- org-specific behavior

If you only read the stack trace, you miss the story.

And that's exactly where most error triage goes wrong.

## The Workflow I Wanted

I created a Claude-local Sentry audit workflow inside the repo so the triage process stops starting from zero every time.

The idea is simple:

1. Claude reads the repo instructions and the local project context
2. it uses Sentry MCP tools to inspect organizations, projects, issues, and events
3. it compares those findings against what the roadmap already says is broken, in-progress, or recently fixed
4. it summarizes the issue in human language instead of just echoing raw Sentry output
5. it tells me what is worth fixing first

The important part is that this is **repo-local behavior**, not just a general-purpose prompt floating in the void.

That means the workflow can inherit project-specific rules:

- how we reason about milestones
- what counts as duplicate work
- what kinds of issues are noisy in this stack
- what improvements were just shipped

That’s where it gets useful.

## Why MCP Matters Here

Without MCP, Claude can only reason over what I paste into chat.

With MCP, it can actually query the monitoring system directly.

So instead of:

> "Here's a screenshot of Sentry, what do you think?"

it becomes more like:

> "Look at the actual issues, inspect the events, compare them with the roadmap and tell me what matters."

That shift is huge.

It means the assistant is no longer pretending to be connected to the system. It actually is.

## The Useful Part: Deduplication Against the Roadmap

This turned out to be one of the most important pieces.

A lot of error triage is fake productivity. You discover an issue, open a ticket, feel organized — and then later realize it was already implicitly covered by a milestone or already explained by a known weak spot.

So I wanted Claude to check:

- is this already covered by a roadmap milestone?
- is it just the expected surface area of a known refactor?
- is this a truly new bug or the same bug wearing a different stack trace?

That sounds small, but it's exactly the kind of judgment call people waste energy on all week.

Once you encode it into the workflow, the system gets calmer.

## What This Actually Improves

This doesn’t magically fix bugs.

What it fixes is the layer *before* fixing bugs:

- issue triage
- prioritization
- duplicate detection
- explanation quality
- remembering what the team already knows

That matters more than it sounds.

Because a noisy system isn't just one with lots of exceptions. It's one where the humans have to keep rebuilding context every time they look at the exceptions.

This workflow reduces that rebuild cost.

## The Bigger Idea

I keep coming back to the same pattern:

AI is most useful when it stops being a chat toy and starts becoming **structured project behavior**.

Not:

- "Claude, what do you think about this error?"

But:

- "When Sentry issues appear in this repo, inspect them this way, compare them against planned work, summarize them like this, and help decide whether they deserve attention."

That’s the difference between a one-off answer and a reusable tool.

And once that behavior lives in the repo, it starts compounding.

Because then the assistant isn't just smart.

It's trained on the system you actually have.

---

*Built as a repo-local Claude workflow on top of Sentry MCP access. Less blank-page triage. More reusable operational judgment.*
