---
layout: post
title: "The wiki is the operating system"
description: "How a Karpathy-style LLM knowledge base became the place where I think, plan, and draft — with agents reading from it instead of starting from zero."
date: 2026-05-07 10:00:00 -0300
categories: [ai, development]
tags: [knowledge-base, agents, workflow, wiki, writing]
image: /assets/img/posts/the-wiki-is-the-operating-system-hero.png
author: leandrotocalini
---

Karpathy posted a thing a while back about LLM knowledge bases — raw sources go in a folder, an agent compiles them into a wiki, you query the wiki later. I read it, shrugged, and a week later I had one.

That was a few months ago. Now it's where I think.

The setup is boring. A folder of markdown files. `raw/` for sources — articles, transcripts, voice notes, chat logs. `wiki/` for compiled pages. Bidirectional wikilinks between everything. A small CLI on top so I don't break the conventions when I'm tired.

What's in it: code projects, personal notes, health notes, roadmaps for stuff I'm building, and the blog itself. Captured context I want the next agent session to inherit. Decisions I made and want to find again. None of it is precious in isolation. All of it together would be painful to lose.

## Raw goes in. Wiki comes out.

The pattern from Karpathy is simple, and that's the point.

I drop things in `raw/` without thinking about structure. A voice note transcribed by my phone. An article I want to remember. A chat where I made a call I'll forget by Tuesday. Naming is `YYYY-MM-DD-slug.md` and a small header. That's the whole capture protocol.

When the pile gets big enough, I run an ingest. An agent reads the raws, decides where each one belongs, updates the right wiki page or creates a new one, fixes the backlinks, commits. I don't write the wiki by hand. The agent does, with rules I've written down for it about voice and structure.

Querying is the trivial part. The wiki is just files, so anything that reads files reads the wiki. Claude reads it. My local agent reads it. I read it on the phone.

## What changed when I started using it

I used to keep ideas in my head and let them rot. Or in a Notion I opened twice a year. Or in a chat where I DMed myself, which is the same thing as forgetting, just slower.

Now ideas land in `raw/` within minutes of having them. Sometimes by voice while I'm walking. Sometimes typed into a chat that I pipe into the capture command. The path from "thought" to "captured" is shorter than the path to forgetting, and that's the only thing that ever mattered.

The thing that surprised me wasn't the capturing. It was what happened *next*.

Because the wiki is a single coherent corpus, agents inherit context for free. When I open a session and ask "help me think about X," I'm not starting from zero. I'm starting from everything I've already written down about X, plus the connected pages, plus the rules of voice and naming and what not to invent.

That's the part Karpathy's tweet didn't quite spell out, at least not for me: the wiki isn't a place to *store* knowledge. It's a place to *think with agents*. Ideas get discussed there. Roadmaps get planned there. Drafts come out of there. The corpus is the system prompt.

## The meta-loop

This post is an example.

I had the thought walking around: I should write about how the wiki actually works for me. I sent a voice note. The transcript landed in `raw/` as a chat capture. Then I asked Claude to draft this post — but not from the voice note alone. From the wiki page about my editorial workflow, the playbook page that defines voice and what to avoid, the example posts, and the raw idea, all bundled into the prompt.

So the draft you're reading wasn't written from a generic "LLM blog post" voice. This is the same loop I started with [I talk. My bot composes.](/vibe-shipping-diary/posts/the-bot-writes/) and the same constraint-heavy writing style that showed up when [Claude Code compressed my CLAUDE.md](/vibe-shipping-diary/posts/claude-md-compression/). It was written from a corpus that already encodes my old slogans, my guardrails, the things I refuse to say. I'll iterate on it like every other post — cut a paragraph that sounds too clean, swap a phrase I already used somewhere, add a fact that's missing. Then commit, push, read it on the phone, find one sentence I want to rewrite.

The point isn't that the agent wrote the post. The point is that I didn't have to re-explain who I am.

## What this isn't

It's not a "second brain" pitch. I'm not selling a stack. There's no SaaS. The wiki is markdown files in a git repo. The agent is Claude plus a small set of skills I've written. The intelligence, such as it is, lives in the rules I wrote down — not in any tool.

It also isn't finished. Pages are stubs. Some backlinks are broken. Some raws have been sitting there for two weeks waiting to be ingested. There's a doctor command that nags me when the structure starts to drift, and I run a maintenance pass when it complains. That's it.

What it is: a memory layer for thinking with agents. Cheap to extend. Boring to maintain. Annoying to live without, once you've had it for a few months.

I don't think in my head anymore. I think in a folder of markdown files.
