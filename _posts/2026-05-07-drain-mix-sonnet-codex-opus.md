---
layout: post
title: "Drain-mix: routing one milestone through Sonnet, Codex, and Opus"
description: "I stopped letting one model do the whole loop. Now Opus plans, Sonnet implements, Codex reviews, Opus fixes and closes — and Hermes tells me what happened."
slug: drain-mix-sonnet-codex-opus
categories: [dev, ai]
tags: [agents, claude-code, codex, workflow, shipping]
image: /assets/img/posts/drain-mix-sonnet-codex-opus-hero.png
author: leandrotocalini
---

For a while, my overnight loop was a single agent doing everything. One model picked up the next milestone, planned it, wrote the code, ran the checks, committed, pushed. It worked. It also drained context, drained money, and drained me when I had to debug what it had done wrong six steps back.

The fix wasn't a smarter prompt. It was a router.

I rewrote the drain so it doesn't ask "what should the agent do next" — it asks "which agent should do this next." The loop is the same shape: read the roadmap, pick the next milestone, run it, push, deploy, move on. What changed is that *inside* a milestone, the work is split across three different models with different jobs.

Opus plans. Sonnet implements. Codex reviews. Opus fixes and closes. Hermes — my local agent — narrates the whole thing into my chat, so I can follow it from the phone without opening a terminal.

## What I had before

One model, one loop. `drain` would walk the roadmap, mark a milestone in progress, plan it, implement it, test it, commit, push. Ship while you sleep, review in the morning.

The honest version: it worked for the easy stuff and broke quietly on the hard stuff. The same model that wrote a hundred lines of code also reviewed those hundred lines. It would happily approve its own diff. When something subtle slipped through — a missing org scope, a race condition, a contract change I didn't catch — I'd find it the next day, two milestones deep, with the offending commit buried under three "fix" commits that didn't actually fix it.

Same brain doing implementation and review. That's not a review. That's a self-grade.

## Drain-mix

The new entrypoint is a thin shell script. It reads the same roadmap, picks the same next milestone, but the work inside is phased:

- **Planning — Opus writes the milestone plan first.** Before implementation starts, the script either reuses an existing `milestones/Mx.yaml` or calls `/plan-milestone` with `MODEL_PLAN=opus`. This is the expensive thinking step: break the milestone into tasks, define execution state, and create the file Sonnet will later consume.
- **Phase 1 — Sonnet implements all planned tasks in batch.** Cheap, fast, broad. It takes Opus's plan file and writes the code for every task in the milestone in one pass. No review yet. No closing yet.
- **Phase 2 — Codex batch-reviews the entire diff.** One shot, against the commit captured before implementation started. Codex isn't asked for style notes. It's asked for runtime bugs, security holes, data corruption, contract violations. Everything else is explicitly off-limits.
- **Phase 3 — Opus fixes the actionable findings.** Only the ones that match the guardrail: real bug, real security issue, real broken contract. Opus also gets the deterministic lint-repair shortcut before being called.
- **Phase 4 — Codex re-verifies if Opus changed anything.**
- **Phase 5 — Opus closes the milestone.** Runs the full gate. Marks tasks done. Commits.

Then the outer drain pushes, deploys, and moves to the next milestone.

The default routing lives in env vars: `MODEL_IMPLEMENT=sonnet`, `MODEL_FIX=opus`, `MODEL_PLAN=opus`, `MODEL_TRIVIAL=sonnet`. That last one matters more than it sounds — marking a YAML status, editing a frontmatter line, committing a status change shouldn't cost Opus tokens. Sonnet does the boring trivial edits.

The loop runs up to thirty iterations, handles `awaiting_human` milestones gracefully, recovers from post-close failures without aborting the drain, and final-pushes at the end.

## Why this changed something

The naive version of "use multiple models" is a mush — ask three of them, average the answers, hope for the best. That's not what this is.

This is a pipeline with separation of duties. Each model has one job and one guardrail. Opus's first job is to plan the milestone into a concrete YAML execution file. Sonnet's job isn't to be smart — it's to convert that clear spec into a working diff, fast and cheap. Codex's job isn't to write code — it's to find what's actually broken in the diff Sonnet just produced, with a hard whitelist of what counts as a finding. Opus's second job isn't to drive — it's to handle the hard fixes and run the gate that closes the milestone honestly.

The model is important. But the routing is the product.

When I was using one model for everything, I was paying Opus prices for trivial commits and accepting Sonnet-grade reviews of Sonnet-grade code. With the mix, the cheap step gets the cheap model, the hard step gets the hard model, and the review step gets a model that didn't write the code it's reviewing. That last one is the actual unlock. A reviewer with no skin in the implementation will flag things the implementer rationalized away.

## Hermes is the part I didn't know I needed

The other half of the change isn't about models. It's about visibility.

The script calls `notifyHermes` through `bin/drain-mix/notify.sh`. Throttled to roughly one message every 45 seconds via a stamp file in `/tmp`, so the chat doesn't turn into a firehose. Every meaningful transition pings: drain start, iteration N of M, milestone X starting, Opus planning, Sonnet implementing, Codex reviewing, Codex findings summary, Opus fixing, push, deploy, awaiting human, error, completion.

Before this, the drain ran in a terminal somewhere and I had two options: stay in front of the laptop and watch a stream of stream-json, or walk away and hope. Neither is a workflow. One is babysitting; the other is a coin flip.

Now I close the laptop and Hermes tells me, in the same chat I use for everything else: *iteration 4/30, M3.7 in progress, Codex found 2 findings, Opus fixing*. Then twenty minutes later: *M3.7 done, pushed, deployed, 6 milestones left*. I read it from a coffee shop. I don't open the laptop unless something says `❌` or `⏸️`.

The drain became something I can watch without watching.

## What this doesn't replace

I want to be careful here, because this is the part where blog posts about agents usually start lying.

This doesn't replace ownership. I still own every milestone. I still write the spec. I still review the diff in the morning. I still make the call when Codex's "finding" is actually fine, and when Opus's "fix" was the wrong fix.

It doesn't replace taste either. The router can route. It can't tell me whether the feature was worth building. It can't tell me whether the architecture is going to survive the next three milestones. It can't tell me when to stop. Those are still my job, and I haven't found a way to delegate them yet — because the moment I do, the router stops being useful and starts being a confident way to ship the wrong thing fast.

What it replaces is narrower than that, and exactly that's why it works: it lowers the cost of turning a written spec into a reviewed, tested, committed diff — and the cost of finding out, from across the room, where the run got stuck.

That's a smaller claim than "AI is transforming software." It's also the only one I can defend with a git log.

## The slogan

The model is important. The routing is the product. The notifier is the difference between a loop and a system.
