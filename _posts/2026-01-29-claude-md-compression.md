---
layout: post
title: "Claude Code Compressed My CLAUDE.md (and It Changed the Loop)"
date: 2026-01-29 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [llms, agents, workflow, docs]
---

I didn’t rewrite my `CLAUDE.md`. Claude Code did.

I gave it a single example of the kind of compression I wanted — a small before/after showing how to turn a paragraph into a constraint — and then I let it run. A while later I had the same document, but at a completely different density: the fluff gone, the intent sharper, the operating rules easier to execute than to debate.

The obvious win is token overhead. Smaller context means faster sessions and less pressure on the window as a conversation grows. But that’s not the part that surprised me. What surprised me is that it didn’t feel like summarization. It felt like refactoring.

I used to treat agent docs like human docs: explain the reasoning, write the why, be generous with nuance. That instinct is polite — and often wrong. An agent doesn’t need persuasion. It needs triggers, constraints, defaults, and pointers to canonical places in the repo. When you give it paragraphs, you’re not giving it clarity. You’re giving it surface area.

A few hours after the compression, I did the most boring test I could think of: I pulled `main` and read the repo the way an agent would. The difference was immediate. The new `CLAUDE.md` didn’t read like a document. It read like a control panel. Short cues. Sharp constraints. Clear “where things live.” Fewer places to hide ambiguity.

One small example: in my Aurum repo I’ve accumulated a handful of “workflow documents” over time — not because I love documentation, but because I’m trying to make the loop cheap. There’s `CLAUDE.md` for conventions, `ROADMAP.md` for committed milestones, `OPEN_TASKS.md` for the active checklist, and `BRAINSTORMING.md` as a scratchpad where ideas can live without becoming commitments. There are integration test scripts under `integration_tests/` so “does this still work?” is a command, not a mood. There are planning branches that merge into `main` when the plan is clear, and feature branches that ship as PRs when the work is real.

When `CLAUDE.md` was bloated, those files existed, but the system felt harder to operate. After compression, the same repo felt more legible: less debate about process, more obvious defaults. It also exposed something else: documentation isn’t just information, it’s an interface to a workflow. Once the rules were compressed, the process became easier to see. Instead of everything being “source of truth,” the repo naturally wanted a single active place for current work, and a simpler rule for when roadmap/status should change. It’s hard to explain, but it’s the same feeling you get after deleting a bunch of dead code: the system becomes more legible.

The most interesting part, though, was how little I had to specify. I didn’t micromanage every section. I provided one exemplar — one demonstration of what “good compression” looks like — and the agent generalized it across the document. That feels like a new kind of leverage. You’re not rewriting the rules; you’re rewriting the pattern of the rules and letting the agent apply it everywhere.

There is a risk here. Compression can delete nuance, and nuance sometimes prevents expensive mistakes. The fix isn’t to keep your `CLAUDE.md` bloated “just in case.” The fix is to pair a tight instruction set with a workflow that catches the important failures: tests by default, integration checks for the scary paths, and explicit pre-deploy guardrails. Short rules are fine as long as reality is still your reviewer.

I used to think the main benefit of agents was speed — generating code faster. Now I think the deeper benefit is consistency: taking a single good pattern and applying it everywhere, across code and process. Compression was the first time I felt that advantage in a non-flashy way.

Constraints are the new code.
