---
layout: post
title: "Milestones, not tasks: how I run my agent overnight"
date: 2026-02-02 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [llms, agents, workflow, shipping]
---

I used to think the hard part of working with agents was prompting. Get the words right, get the output right. It seemed like a skill you could grind: learn the tricks, master the phrasing, unlock the magic. And for quick tasks, that's mostly true. But for anything that spans more than a single conversation — a real project, with real scope — prompting turns out to be the least interesting part.

The real leverage is in how you structure the work before the conversation starts.

Over the past few weeks, I've been building an iOS app with an LLM agent doing most of the implementation. Not as a copilot suggesting lines while I type, but as something closer to a remote junior developer: I describe what needs to happen, it figures out the code, runs the tests, commits it, pushes the branch, and moves on to the next thing. I review in the morning.

The setup is a bit unusual. I have two agents working together. One lives on my phone — a WhatsApp bot I use to think out loud, define milestones, and manage the roadmap from wherever I am. The other runs on my Mac — a coding agent that reads the repo, writes the code, and executes the overnight runs. When I'm on the couch sketching out a feature in voice notes, the first agent turns that into a structured milestone. When I go to sleep, the second agent picks it up and implements it.

The surprising part wasn't that it worked — plenty of people have shown agents writing code — it's that it worked *better* when I wasn't there to interfere.

This post is about the system that makes that possible.

## Why milestones instead of tasks

When I started using agents for implementation, I gave them tasks the way I'd write them for myself. "Add voice input to the app." "Fix the dashboard layout." "Handle multi-currency conversion." These seemed reasonable. They're the same mental units I'd use in a todo list, the same things I'd tell a coworker.

The problem is that tasks like these are full of implicit knowledge. What does "fix the dashboard layout" actually mean? Which files? Which components stay, which change? What's the acceptance bar? When is it done — when it looks good to me? When it compiles? When I stop having opinions?

I knew the answers to all of these questions. But the agent didn't. And that meant it had to guess, or ask, or make assumptions that I'd have to correct later. The result was a lot of back-and-forth. I'd say "add voice input," the agent would make something, I'd say "no, not like that," and we'd iterate until I was happy. It worked, but it didn't feel like leverage. It felt like I was still in the loop for every decision, just with extra steps.

So I switched to milestones.

A milestone isn't a task. It's a contract. It has a name — M3.2, M4.1, whatever fits the roadmap — a clear objective, explicit acceptance criteria, and a definition of done that doesn't depend on my taste in the moment. The agent doesn't need to interpret my intent. It reads the spec, executes, and either passes or fails.

Here's what a typical milestone looks like:

```
### M3.2 — Input bar redesign

**Objective:** Replace floating action button with a persistent input bar.

**Acceptance criteria:**
- Input bar visible at bottom of Home tab at all times
- Text field with microphone and camera icons
- Tap text field → keyboard appears
- Tap mic icon → voice recording starts
- Tap camera icon → photo capture flow
- Bar hides gracefully when keyboard is active
- Spring animations on all transitions (response: 0.3, dampingFraction: 0.7)
- Works correctly in both light and dark mode
```

When the agent reads this, there's no ambiguity. It knows what to build, what to check, and when to stop. The milestone becomes a self-contained unit of work that can be executed without asking me anything.

The key insight is that writing a good milestone takes me maybe ten minutes, but it saves hours of back-and-forth and produces better results. The constraint forces clarity. If I can't write clear acceptance criteria, it usually means I haven't thought the feature through — which is useful information to have *before* the agent starts coding.

## The overnight loop

Once milestones became the unit of work, the next step was obvious: what if the agent could execute *all* of them in sequence, without supervision?

That's where the "drain" command came from. It's a custom script I wrote that does one thing: drains the backlog. It reads my roadmap, finds all the milestones marked as planned, and asks me a single question: "Found 7 milestones. Run autonomously?" If I say yes, it goes. No more questions, no more check-ins, no more waiting for my approval. It runs until everything is done or something breaks.

For each milestone, the loop is the same:

First, the agent enters a planning phase. It reads the milestone spec, explores the relevant parts of the codebase, and designs an approach. This isn't just "start coding" — it's more like an architect reviewing the blueprints before construction starts. The plan gets auto-approved (no human in the loop), and then execution begins.

Next, implementation. The agent writes the code, following the project's conventions (which are documented in a separate file it reads at the start). It makes the changes, adds the tests if needed, and wires everything together.

Then, testing. For backend changes, it runs the test suite. For iOS changes, it builds the project and makes sure nothing broke. If tests fail, the whole process stops and I get a report.

If tests pass, it updates the documentation: the roadmap status changes from 📝 (planned) to ✅ (done), and a changelog entry gets added. Then it commits — one commit per milestone, with a clear message — and pushes the branch immediately.

Then it moves on to the next milestone and repeats.

I wake up to a branch with five or six features implemented, tested, documented, and ready for review. The git history is clean. The tests pass. The diff makes sense. I still review everything — I'm not blindly merging agent code — but the hard part is done. I'm reading and approving, not writing and debugging.

The first time I ran this overnight, I didn't expect it to work. I figured I'd wake up to a disaster: half-finished features, cryptic errors, maybe a corrupted repo. Instead, I found exactly what I described. Clean commits. Passing tests. A changelog that read like a release notes draft. It felt like cheating.

## What makes it work

This system isn't magic. It works because the structure does most of the heavy lifting, and the agent just follows the rails.

Milestones are atomic. Each one is small enough to complete in 15 to 60 minutes, with clear boundaries. The agent never has to make judgment calls about scope, because the scope is defined in the spec. If a milestone is too big or too vague, the system breaks down — which is a feature, not a bug. It forces me to decompose properly.

The roadmap is the source of truth. I use simple status emojis — ✅ for done, 🚧 for in progress, 📝 for planned — that make it trivial for the agent to parse. It knows what's next without asking. When a milestone is done, the status updates automatically. I never have to sync state between my head and the codebase.

Conventions are documented. I keep a file that encodes all the project's rules: naming conventions, architecture patterns, testing approach, commit message format, even the animation curves to use. The agent reads it once and applies it everywhere. This is what keeps the generated code from feeling like a Frankenstein patchwork — it all follows the same style, because the style is explicit.

Commits happen per milestone. If something breaks at milestone 4, I don't lose milestones 1, 2, and 3. Each push is a checkpoint. I can review incrementally, revert surgically, or cherry-pick if I need to.

And here's the counterintuitive part: the system works *because* I'm not available to answer questions. When I'm in the loop, the agent can ask me things. "Should this be a modal or a sheet?" "What color should the error state be?" "Is this edge case important?" These questions feel productive, but they're actually a symptom of ambiguity in the spec. When I'm asleep, the agent can't ask — so it has to figure it out from the documentation. Which means the documentation has to be good enough to answer those questions.

The result is a feedback loop. Every time the agent gets stuck on something ambiguous, I don't just fix the immediate problem — I clarify the milestone or the conventions so it won't happen again. Over time, the system gets more autonomous, not less. The docs get better. The specs get sharper. The overnight runs get longer.

---

I want to be clear about what this doesn't replace. It doesn't replace thinking about architecture. It doesn't replace making design decisions. It doesn't replace the taste that determines whether something is good or just functional. Those are still my job.

What it replaces is the grind of turning decisions into code. The part where you already know what needs to happen, and you just need someone to type it out, test it, and commit it. That part used to take hours. Now it takes overnight — literally — and I'm not even awake for it.

When you can batch the grind into a sleep cycle, the economics of building change completely. You can be more ambitious about scope. You can afford to do things "properly" instead of cutting corners. You can wake up to a feature branch that's ready for review instead of a todo list that's ready for another long day.

Ship while you sleep. Review in the morning. Repeat.
