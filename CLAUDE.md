# Vibe Shipping Diary — Writing Guide

## Voice & Style

Reference post: `_posts/2026-02-02-overnight-agent-runs.md`

This is the gold standard. Match its tone, structure, and length for all future posts.

### Characteristics

- **Reflective, not tutorial** — Essays from experience, not how-to guides
- **Personal but technical** — First person, real examples, no fluff
- **Fluid prose** — Paragraphs that flow, not bullet-point lists
- **~1500 words** — Long enough to develop ideas, short enough to finish
- **3 sections with subtitles** — Clear structure without being rigid
- **Strong opener** — Hook in the first paragraph
- **Memorable closer** — End with something quotable

### What to avoid

- Twitter thread energy (short punchy lines, numbered lists)
- Generic AI hype or "10x productivity" claims
- Tutorial format ("Step 1: do this")
- Corporate voice or hedging language
- Mentioning product names unless relevant (abstract the learnings)

### Workflow

1. Leandro talks (Spanish, WhatsApp, messy ideas)
2. We iterate until the structure is clear
3. Draft in `_drafts/`
4. Review and refine
5. Move to `_posts/YYYY-MM-DD-slug.md`
6. Commit + push → GitHub Pages deploys

## Frontmatter Template

```yaml
---
layout: post
title: "Title here"
date: YYYY-MM-DD 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [relevant, tags, here]
---
```

## Commit Messages

- `feat: new post - {title}` — New posts
- `fix: {description}` — Edits to existing posts
- `chore: {description}` — Config, maintenance
