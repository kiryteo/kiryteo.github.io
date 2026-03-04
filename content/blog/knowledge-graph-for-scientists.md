---
title: "How I Built a Knowledge Graph for 80 Scientists"
date: 2026-03-03
description: "Building a production knowledge graph at the Allen Institute for Cell Science — from brute-force LLM matching to graph-native intelligence."
tags: ["knowledge-graphs", "neo4j", "embeddings", "llm", "science"]
ShowToc: true
TocOpen: true
---

At the Allen Institute for Cell Science, about 80 researchers work across cell biology, microscopy, data science, and engineering. New papers, preprints, and announcements appear daily that are relevant to subsets of the team — but nobody has time to monitor everything. People miss papers they should read, don't realize a colleague is working on something adjacent, and have no systematic way to spot gaps in the team's collective expertise.

I built **CellGraph** to solve this. It's a knowledge graph that connects people to papers, topics, and projects — then automatically surfaces what matters, what's missing, and what's emerging.

This post covers the technical journey: the naive first approach, the insight that made it work, and the system running in production today.

## Why a Graph

The core data model is relationships. A person *has expertise in* topics. A paper *is about* topics. A person *works on* a project. A project *focuses on* topics. These aren't flat — they're a web of connections, and the interesting signals emerge from the *structure* of that web.

The schema is compact:

- **5 node types**: Person, Content, Topic, Project, Feed
- **7 relationships**: `EXPERT_IN`, `WORKS_ON`, `AUTHORED`, `FOCUSES_ON`, `ABOUT` (scored), `MATCHED_TO` (scored + classified), `FROM_FEED`
- **3 vector indexes**: On Person, Content, and Project embeddings (768-dim, cosine similarity)

Neo4j was the right choice here because it supports native vector indexes alongside graph traversal — no separate vector database needed. A single Cypher query can combine "find semantically similar papers" with "traverse expertise relationships" in one pass.

## The Naive Approach (v1)

The first version was straightforward: when new content arrives, compute its SPECTER2 embedding, compare it against every person's embedding, then call an LLM to validate each potential match.

[SPECTER2](https://github.com/allenai/specter2) is Allen AI's scientific paper embedding model — purpose-built for this domain and significantly better than general-purpose embeddings for academic content.

Here's the problem: with ~80 people and a batch of new papers, this meant ~2,000 LLM calls per run. Each call validated whether a person-paper match was real or a false positive from embedding similarity. Runtime: 2-4 hours. Cost: nontrivial.

It worked, but it didn't scale, and the LLM was doing work that the data structure could do better.

## The Insight: Let the Graph Do the Matching

The key realization was that the graph already encodes matching logic — we were just asking an LLM to approximate what Cypher could compute directly.

Consider: if a paper is *about* "endoplasmic reticulum dynamics" and a person is an *expert in* "endoplasmic reticulum dynamics", that's a match. You don't need an LLM to tell you this. The graph traversal `Person→EXPERT_IN→Topic←ABOUT←Content` finds it in milliseconds.

Version 2 uses two-tier matching with zero LLM calls:

**Tier 1 — Graph traversal**: Pure Cypher. Walk the topic relationships between people and content. Use the scored `ABOUT` relationships (assigned by Allen AI's ASTA research tools) as relevance weights.

**Tier 2 — Profile-based search**: Construct a search query from each person's expertise profile, search for papers matching that profile, and ingest any new discoveries. Still zero LLM calls — the profile itself is the query.

Result: **~0 LLM calls, ~5 minutes.** The same quality of matching, orders of magnitude faster.

## Pulse: Strategic Intelligence

Matching papers to people is useful but not novel. The interesting layer is **Pulse** — a classification system that tells you *why* a match matters.

Every matched content item gets a "pulse category":

| Category | What it means | How we detect it |
|----------|--------------|-----------------|
| **Reinforcement** | Validates existing expertise | Topic has 2+ experts on the team |
| **Blind Spot** | Relevant area, nobody covers it | Topic has 0 experts |
| **Bridge** | Connects different projects | Content relates to topics from 2+ projects |
| **Frontier** | Emerging area outside current scope | No existing topic relationships |

These categories are computed by graph structure, not by LLM classification. A "blind spot" is literally a topic node with no `EXPERT_IN` edges. A "bridge" is a content node with `ABOUT` edges pointing to topics owned by different projects. The graph *is* the classifier.

### The Knowledge Surface

Before settling on graph-native classification, I explored a geometric approach: modeling the team's collective knowledge as a shape in embedding space.

The idea: compute the centroid of all person embeddings, measure the "coverage radius" (based on pairwise distances), then classify new content by where it falls relative to this surface.

- Content near a person but far from the centroid → **blind spot** (someone should care, but the team as a whole doesn't cover it)
- Content near 2+ people whose embeddings are far apart → **bridge** (connecting different clusters of expertise)
- Content beyond the coverage radius → **frontier** (truly novel to the team)

This was a compelling metaphor, and it worked — but the graph traversal approach was simpler, more interpretable, and didn't require tuning geometric thresholds. The lesson: when your data is already structured as a graph, graph operations beat geometric operations on graph-shaped problems.

## Content Discovery

CellGraph monitors four sources:

1. **RSS feeds** — bioRxiv, Nature Cell Biology, Allen Institute blog, and several others
2. **Semantic Scholar** via ASTA — structured paper search through Allen AI's research tools
3. **bioRxiv preprints** — direct API for the latest preprints
4. **Manual/admin** — through the web dashboard

All sources flow through a unified ingestion pipeline: auto-compute SPECTER2 embeddings, deduplicate by URL, create `ABOUT` relationships to topics, and run matching. A single `bootstrap.py` script seeds the graph from scratch — 12 topics, 3 projects, 7 feeds — and populates everything in one run.

## The Weekly Pulse Report

Every week, CellGraph generates a structured intelligence digest:

1. **Blind spots** — research areas relevant to the team but uncovered by any member
2. **Bridges** — papers connecting different projects' domains
3. **Frontiers** — genuinely new areas appearing at the edges
4. **Momentum** — topics seeing increasing publication activity
5. **Recommended actions** — what to read, who to talk to, what to investigate

The report is rendered as HTML and surfaced through the web interface. This is the one place where an LLM call remains — generating natural-language recommendations from the structured graph signals.

## Architecture Choices Worth Noting

**Neo4j vector indexes instead of a separate vector DB.** Fewer moving parts. Vector similarity search and graph traversal in one query language. This matters more than you'd think — every additional service is a deployment headache and a failure mode.

**SPECTER2 over general embeddings.** Domain-specific embeddings dramatically improve recall for scientific content. A general model might think two papers are similar because they both mention "cells" — SPECTER2 understands the difference between stem cells and fuel cells.

**HTMX + D3.js over a React frontend.** The web interface uses server-rendered HTML with HTMX for interactivity and D3.js for graph visualization. No build step, no npm, no component library. For an internal tool serving 80 people, this is the right level of complexity.

**Protocol-based dependency injection.** The embedder uses a Python `Protocol` type — any class implementing `embed()` works. The test suite (958 tests) uses a mock embedder that returns deterministic vectors. This pattern is underrated for ML systems where you need to test without GPU access.

## What I'd Do Differently

**Start with the graph, not the embeddings.** My instinct was to reach for vector similarity first and add structure later. In retrospect, the relationships between entities *are* the primary signal, and embeddings are a useful supplement — not the other way around.

**Instrument LLM costs from day one.** The v1→v2 transition happened because I noticed the cost and latency were unsustainable. If I'd tracked per-call costs from the start, I would have been motivated to find graph-native solutions sooner.

**Design for the weekly report, not the daily match.** The most valuable output isn't "here's a paper for you" — it's "here's what the team is missing." I should have centered the product around the Pulse report from the beginning, rather than treating it as a feature added later.

## The Bigger Picture

CellGraph is a specific instance of a general problem: how do you make a team's collective knowledge legible, navigable, and strategically useful? The techniques — knowledge graphs, scientific embeddings, graph-native classification — transfer to any organization where people produce and consume research.

The code is at [github.com/kiryteo/cellgraph](https://github.com/kiryteo/cellgraph). If you're building something similar or thinking about knowledge management for research teams, I'd love to hear about it.
