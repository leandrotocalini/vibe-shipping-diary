---
layout: post
title: "Claude Code compressed my CLAUDE.md"
date: 2026-01-29 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [llms, agents, workflow, docs]
---

I didn’t manually rewrite my `CLAUDE.md`.

Claude Code did — after I gave it a single example of the compression style I wanted.

The example was basically: take a long “policy paragraph” and turn it into a short constraint that still changes behavior. Something like: *don’t explain everything — encode the trigger and the rule.*

Then I told it to apply that pattern everywhere.

The result was… aggressive:

- 1525 lines → 368 lines (**-75.9%**)
- Estimated token overhead: ~15,000 → ~5,000 (**~ -67%**)

## What I actually asked it to do

Not “summarize this document.”

More like: *refactor the instruction set.*

Keep the critical rules, delete the speeches, compress to signals:

- triggers (“if X, do Y”)
- constraints (“never do Z”)
- defaults (“prefer A”)
- pointers (“the canonical file is here”)

In other words: make it executable.

## Why the reduction is the point (not the flex)

Yes, fewer tokens is nice. But the bigger win is **less ambiguity**.

A bloated `CLAUDE.md` doesn’t just cost tokens — it dilutes attention. Everything looks equally important, so the agent (and future me) can’t tell what matters in the moment. It becomes a messy attic of “rules.”

After compression, it reads like a control panel. It’s easier to follow, easier to trust, and harder to misinterpret.

## What not to do

Compression can backfire if you use it to erase *knowledge*.

A good example of the failure mode is "context collapse": you start with concrete, testable rules, then you keep rewriting them until they degrade into generic advice ("write quality code"). It’s tidy, but it’s worse than useless — because you silently lose the details that actually made the workflow reliable.

The safer approach is: compress prose, not domain knowledge. Keep defaults short, but treat hard-won lessons as append-only, itemized playbook entries (ideally with IDs and clear "when to apply" conditions).

For example, don’t let this:

> "Validate `default_currency` against the whitelist (USD, ARS, PYG, EUR, BRL). Return HTTP 400 on invalid input. Include request_id in logs. Add integration tests for both valid and invalid cases."

degrade into:

> "Handle currency correctly. Add tests."
## The takeaway

I used to think agents were mainly about generating code faster.

Now I think the real advantage is that they can take one good pattern — one clean example of how you want things done — and apply it everywhere, consistently.

Constraints are the new code.
