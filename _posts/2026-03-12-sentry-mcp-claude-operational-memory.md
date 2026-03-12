---
layout: post
title: "Sentry MCP + Claude: Finding bugs and turning them into milestones"
date: 2026-03-12 00:30:00 -0300
categories: [ai, development]
image: /assets/img/posts/sentry-mcp-claude-hero.png
---

I connected Sentry to Claude through MCP. Now instead of staring at dashboards myself, I ask Claude to go look at what’s broken, find performance problems, and come back with a plan.

The interesting part isn’t the connection itself — it’s what happens when the agent can cross-reference errors against the roadmap and create new milestones from what it finds.

## The setup

My system is a multi-org operations platform for sawmills. Workers report through WhatsApp — text, voice notes, photos — and AI turns that into structured events. There’s a lot of moving pieces: conversations, classification, learning state, org-specific behavior. When something breaks, the stack trace alone doesn’t tell you much.

I added the [Sentry MCP server](https://github.com/getsentry/sentry-mcp) to my Claude Code config. That gives Claude direct access to inspect organizations, projects, issues, and events — no screenshots, no copy-pasting.

## The workflow

I wrote a simple audit prompt that lives in the repo. When I run it, Claude:

1. Pulls the current issues from Sentry
2. Reads the roadmap to see what’s already planned or recently shipped
3. Filters out the noise — duplicates, known weak spots, things already covered by existing milestones
4. Flags the real problems: new bugs, performance regressions, patterns I hadn’t noticed
5. Writes new milestones for whatever deserves attention

That last step is the one that changed things for me. Instead of ending with "here’s what’s wrong," it ends with "here’s the milestone to fix it." Same format I use for my [overnight agent runs](/dev/ai/2026/02/02/overnight-agent-runs.html). Which means I can go from Sentry alert to implemented fix without writing specs by hand.

## What it actually looks like

Last week I ran the audit and Claude found three things worth acting on:

- A timeout in the voice note transcription pipeline that only showed up under specific org configurations. I knew about the timeout but didn’t realize it was org-specific. Claude spotted the pattern across multiple events.
- A slow query in the digest generation that was getting worse as one org’s data grew. Not an error — a performance trend. Claude flagged it because the P95 had shifted over the past two weeks.
- A classification edge case where the same message was being processed twice. Already had a milestone for idempotency, but it didn’t cover this specific path. Claude updated the existing milestone instead of creating a duplicate.

For each one, it wrote a milestone with acceptance criteria, checked it wasn’t duplicating planned work, and added it to the roadmap. I reviewed, adjusted a couple of details, and queued them for the next overnight run.

## Why this matters

The boring version of this workflow is: paste errors into chat, get explanations back. I’ve done that. It’s fine for one-off debugging but it doesn’t compound.

This version compounds because the agent knows the project. It reads the roadmap, the conventions, the recent changes. So when it finds a bug, it can tell me whether it’s new or just the expected surface area of something I already know about. And when it creates a milestone, it follows the same structure the rest of the system expects.

The loop becomes: monitor → triage → plan → implement → monitor. And the agent can handle the first three steps without me.

---

*Sentry MCP gives Claude eyes on the system. The repo gives it memory. The milestones give it something useful to do with both.*
