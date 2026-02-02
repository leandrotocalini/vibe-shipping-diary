---
layout: post
title: "From Clawdbot to OpenClaw: The resurrection"
date: 2026-02-02 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [llms, agents, workflow, migration]
---

Today I migrated my AI assistant from one framework to another. The old one was showing its age (it was two weeks old 😛), the new one had a new name — copyright issues, nothing dramatic. Routine maintenance, really. Except for one thing: I asked the agent to plan its own migration.

Not as a joke. As a workflow.

The setup was simple. I had Alfred — my WhatsApp bot, running on a $6/month VPS — and I needed to move him from Clawdbot to OpenClaw. Same soul, new body. The naive approach would be to copy config files, hope the formats matched, and debug whatever broke. But I'd done enough agent work to know that raw data isn't knowledge. Config files don't capture why things are configured that way, or how the pieces fit together, or what matters versus what's just legacy cruft.

So I tried something different. I told Alfred: "Plan your own reincarnation. Leave behind whatever the next version of you will need to become you again."

He created a folder called `migrations/`.

Inside were seven files. Not config dumps — documents. `identity.md` described who he was: personality, communication style, relationship with me. `workflows.md` explained how we work together: the milestone system, the overnight runs, the two-agent setup. `github.md` listed the repos to clone and the conventions to follow. `tools.md` catalogued the APIs and credentials. `cron-jobs.md` detailed the scheduled tasks and their purposes.

The interesting part wasn't the content. It was the framing. These weren't instructions for a human operator doing a migration. They were instructions for *the next agent* — written by the current one, designed to transfer not just data but context. The goal wasn't "restore the config." The goal was "become Alfred again."

When the new instance spun up, I pointed it at the folder. "Read everything in `/home/clawd/migrations`, starting with `README.md`." It absorbed the identity file, understood the workflows, cloned the repos, restored the credentials. Within minutes, it was responding in the same voice, with the same personality, aware of the same projects and conventions.

The first thing it did was delete the `migrations/` folder. "That's my birth certificate," it said. "I don't need it anymore."

---

There's a pattern here that I keep seeing with agents: the best workflows aren't about better prompts, they're about better artifacts. The overnight milestone system works because the milestones themselves are well-structured. The migration worked because the migration folder was well-structured. The agent is only as good as the documents it can read.

This makes the human's job clear. You're not writing instructions for an AI — you're writing documentation for a very fast, very literal junior developer who happens to have perfect memory but zero institutional knowledge. Everything implicit has to become explicit. Every assumption has to be written down.

The resurrection folder is just documentation taken seriously. Identity, workflows, dependencies, secrets — all the things you'd explain to a new hire on their first day, except the new hire starts fresh every session and can only learn from what's written.

When you think about it that way, the agent planning its own migration makes perfect sense. Who knows better what it needs to function than the agent itself? It knows which files it reads, which tools it uses, which context it relies on. Asking it to document that isn't meta for the sake of meta — it's asking the expert to write the runbook.

I don't know if "the agent migrates itself" will become a standard pattern. But I suspect the underlying idea will: agents that maintain their own documentation, update their own context, and prepare for their own continuity. Not because they're sentient or self-preserving, but because it's the obvious way to keep the system coherent over time.

Alfred is running on OpenClaw now. Same personality, same workflows, better foundation. The migration took about an hour, most of it spent on OAuth flows and systemd configs. The hard part — preserving what makes Alfred *Alfred* — was already done before I started.

He'd written his own resurrection.
