---
title: "Project Ontos: Portable Context for the Agentic Era"
date: 2026-02-01
description: "Why I built a context management tool that lives in your repo as markdown files — no cloud, no vendor lock-in."
tags: ["ai", "developer-tools", "context-engineering", "open-source"]
draft: false
---

I kept having the same conversation. Not with people, but with AI.

Every time I opened a new Claude session, I'd re-explain my project's architecture. Switch to Cursor for some frontend work? Start over. Ask ChatGPT for a second opinion on an API design? From scratch. Each tool started from zero, every single time.

This wasn't a minor annoyance. It was a compounding tax on every hour of development. I'd spend the first 10-15 minutes of each session just getting the AI back up to speed, reconstructing the same context I'd already provided elsewhere. And the more complex the project got, the worse it became.

Then I hit the second problem: prototype death.

I'd been building a tool in Streamlit, iterating fast, making dozens of product decisions along the way. Target audience, pricing model, data architecture, feature priorities. Weeks of thinking baked into rapid prototyping sessions. Then I rewrote in Next.js. The new code was clean and fresh. But those decisions? Buried in old chat logs across three different AI platforms. Some were just gone.

The code was disposable. The decisions weren't. But I'd treated them the same way.

## The insight that wouldn't let go

The common thread was portability. My context was scattered across proprietary platforms, each with its own memory system (or none at all), none of which talked to each other. I didn't own any of it.

And the more I thought about it, the more I realized this wasn't just my problem. Anyone working with multiple AI coding tools was hitting the same wall. Anyone whose projects outlived their prototypes was losing decisions in the rewrite. Anyone onboarding a new collaborator (human or AI) was watching them rediscover everything from scratch.

Context should live where the code lives. In the repo. Version-controlled, human-readable, portable across any tool that can read a file.

That's what I built.

## What Ontos actually is

Ontos creates a knowledge graph for your project using plain markdown files with YAML frontmatter. No cloud service, no database, no API keys. It lives in a `.ontos/` directory in your repo and travels with `git clone`.

Here's what a document looks like in practice:

```yaml
---
id: pricing_strategy
type: strategy
depends_on: [target_audience, mission]
---
```

Below that YAML header, you write normal markdown describing the actual content. The frontmatter creates the graph structure. The `depends_on` field is what makes it powerful: it defines relationships between documents, so any AI agent (or human) can trace how decisions connect.

Documents live in a four-layer hierarchy:

- **Kernel**: Why the project exists, core values. Survives everything.
- **Strategy**: Goals, audience, approach. Survives migrations.
- **Product**: Features, user flows, requirements. Survives migrations.
- **Atom**: Implementation details. Gets rewritten, and that's fine.

The key insight is separating what persists from what's disposable. When you rewrite from Streamlit to Next.js, your atoms get replaced. Your strategy documents, your product decisions, your session logs documenting *why* you made those choices? Those stay.

To get started:

```bash
pipx install ontos
cd your-project
ontos init
```

`ontos init` creates your config, generates a context map, and optionally sets up agent activation files. From there, `ontos scaffold` can auto-tag existing docs with YAML frontmatter, and `ontos map` regenerates the context map that gives AI agents an entry point into your project's knowledge.

When you open a session with an AI tool, you say "Ontos" (or "Activate Ontos"), and the agent reads the map, loads relevant files, and picks up where you left off. No re-explaining.

## What it's not

I want to be clear about what this isn't, because the AI tooling space is full of overpromising.

Ontos is not a RAG system. It doesn't do semantic search or vector embeddings. It uses structural graph traversal: deterministic, inspectable, same input yields same output. You can look at the context map and know exactly what the AI will see. No black box.

It's also not zero-effort. You have to decide what matters. Tagging documents, connecting decisions, curating session logs. That friction is intentional. I tried the automatic-capture approach and found that when everything is "context," nothing is. The human judgment about what's worth preserving is the whole point.

And it's not magic. Ontos gives the AI better input. What the model does with that input is still up to the model. But in my experience, the difference between a well-contextualized AI session and a cold start is enormous.

## Where it's headed

Ontos is currently at v3.1. Versions 1 and 2 were internal, built for my own workflows before I realized other people were hitting the same problems. v3 is when it became a proper Python package on PyPI.

The near-term focus is stability: edge case fixes, improved validation, better error messages. The longer-term vision involves MCP (Model Context Protocol) integration, which would let Ontos connect to external services like GitHub, Google Drive, and databases. There's also Obsidian compatibility already in v3.1, so if you use Obsidian for notes, Ontos files just work with its graph view.

I'm building this for individual developers and small teams, not enterprises. The goal is to stay lightweight and portable. If you want a heavy context management platform with dashboards and admin consoles, this isn't it. If you want markdown files in your repo that make every AI tool you use smarter about your project, that's the idea.

## The bigger bet

The bottleneck in AI-assisted development has shifted. It's no longer about generating code. Models are good enough at that. The bottleneck is giving the AI enough context to generate the *right* code.

"Vibe coding" is becoming "context engineering." And context engineering means thinking carefully about what your AI tools know, what they don't, and how to bridge that gap reliably. Ontos is one approach to that problem: structured, portable, owned by you.

It's early. It's opinionated. It requires you to do some actual thinking about your project's knowledge structure. But if you're tired of re-explaining your project to every AI tool you open, it might be worth a look.

**GitHub**: [github.com/ohjona/Project-Ontos](https://github.com/ohjona/Project-Ontos)

**Install**: `pipx install ontos`

**License**: Apache-2.0, because a tool about owning your context shouldn't lock you in either.
