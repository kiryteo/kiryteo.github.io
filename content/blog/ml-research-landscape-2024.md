---
title: "What 6,295 ML Papers Tell You About Where Research is Heading"
date: 2026-03-02
draft: true
description: "Clustering ICLR and NeurIPS papers with TF-IDF, UMAP, and KMeans to find the real structure of ML research in 2024."
tags: ["machine-learning", "research", "data-analysis", "NeurIPS", "ICLR"]
ShowToc: true
TocOpen: true
---

I recently analyzed **6,295 accepted papers** from ICLR 2024 and NeurIPS 2024 to understand what the ML research community is actually working on — not what Twitter thinks it's working on, but what the publication record shows.

The method: scrape paper titles and abstracts, vectorize with TF-IDF (8,611 features from unigrams + bigrams), reduce dimensions with UMAP, and cluster with KMeans. Simple, reproducible, no deep learning needed to analyze deep learning research.

Here's what I found.

## The Method

I tried HDBSCAN first for clustering (min_cluster_size=15, min_samples=5), but it produced 34% noise points — too many papers left unassigned. KMeans with k=28 gave cleaner assignments and more interpretable clusters.

The pipeline:
1. **Scrape** paper metadata (title, abstract, author keywords, venue type: oral/spotlight/poster)
2. **Vectorize** with TF-IDF, filtering to unigrams and bigrams
3. **Reduce** to 50 dimensions with UMAP (for clustering) and 2 dimensions (for visualization)
4. **Cluster** with KMeans (k=28), then manually name each cluster based on top TF-IDF terms and author keywords
5. **Group** clusters into macro-areas based on thematic similarity

I also tracked "elite rate" — the percentage of oral + spotlight papers in each cluster — as a proxy for what the review committees consider highest-impact work.

## Five Macro-Areas

The 28 clusters naturally group into five themes:

| Macro-Area | Papers | Share |
|-----------|--------|-------|
| **Language & Foundation Models** | 561 | 24.8% |
| **Core ML & Optimization** | 638 | 28.2% |
| **Generative Models & Vision** | 521 | 23.1% |
| **Trustworthy & Robust ML** | 262 | 11.6% |
| **Decision-Making & Applications** | 278 | 12.3% |

The headline: **a quarter of accepted papers are about language models**, but the largest macro-area is still Core ML & Optimization. The field hasn't fully tipped into an LLM monoculture — there's substantial work in graph neural networks, optimization theory, dynamical systems, causal inference, and self-supervised learning that has nothing to do with language.

## What the Committees Reward

Elite rate (oral + spotlight percentage) reveals what reviewers consider the strongest work, independent of volume:

**Highest elite rates:**
- Embodied AI, Robotics & Planning: **31.0%** (58 papers, but nearly a third are oral/spotlight)
- Optimization Theory & Convergence: **25.2%**
- Fairness & Bias in ML: **25.0%**
- RL & Policy Optimization: **24.4%**
- 3D Vision, NeRF & Scene Understanding: **24.0%**

**Lowest elite rates:**
- Graph Neural Networks: **10.9%** (101 papers, only 11 oral/spotlight)
- Continual Learning & OOD Detection: **14.3%**
- Model Compression: Pruning & Quantization: **14.6%**
- Time Series Forecasting & Contrastive Learning: **16.0%**

The contrast between Graph Neural Networks (101 papers, 10.9% elite) and Embodied AI (58 papers, 31.0% elite) is striking. GNNs have high volume but low distinction — lots of incremental work. Embodied AI has lower volume but much higher signal — the papers that get in tend to be strong.

## Clusters Worth Watching

### Molecular Design & Drug Discovery (45 papers, 17.8% elite)

Small but growing. Top keywords: protein design, diffusion models, reinforcement learning, drug discovery. This is where AI x Science lives at these venues — equivariant networks and diffusion models applied to molecular structures. The cluster is punching above its weight relative to its size.

### Dynamical Systems & Neural Dynamics (87 papers, 21.8% elite)

Includes computational neuroscience, implicit neural representations, and dynamics-aware architectures. This cluster bridges pure ML theory with scientific applications. High elite rate suggests the committee values this interdisciplinary work.

### Self-Supervised Learning & Domain Adaptation (109 papers, 22.0% elite)

Still substantial at 109 papers. SSL isn't hype anymore — it's infrastructure. The author keywords show convergence: self-supervised learning, representation learning, domain adaptation, dataset distillation. This is the "make models learn from less labeled data" cluster, and it's healthy.

### RLHF, Alignment & Human Feedback (79 papers, 16.5% elite)

Notably, **zero orals**. 79 papers and not a single one was selected for oral presentation. This is the alignment tax: the topic is important and well-funded, but the committee isn't rewarding it with the highest distinction. Possible interpretation: lots of application papers, fewer novel contributions.

## What's Missing

The analysis reveals gaps as much as strengths:

**Autonomous scientific research** doesn't have its own cluster. Papers about AI systems that design and run experiments are scattered across RL, molecular design, and embodied AI. This area is pre-cluster — emerging but not yet coherent enough for a distinct community.

**AI for biology beyond drug discovery** is underrepresented. Cell biology, microscopy, ecology, neuroscience applications are scattered. The molecular design cluster captures chemistry-adjacent work, but computational biology more broadly doesn't have critical mass at these venues.

**Infrastructure and systems papers** are thin. The clusters focus on algorithms and applications. Papers about training infrastructure, data pipelines, evaluation frameworks, and deployment — the things that make ML work in practice — are underrepresented relative to their importance.

## The Positioning Implications

If you're thinking about where to contribute:

1. **High-volume, low-distinction areas** (GNNs, model compression, time series) are crowded. Incremental improvements won't stand out. You need a novel angle — an application domain, a theoretical contribution, or a systems insight.

2. **Low-volume, high-distinction areas** (embodied AI, fairness, molecular design) are where strong work gets rewarded. These areas have fewer submissions but higher acceptance standards. The bar is quality, not quantity.

3. **Cross-cutting themes** — papers that combine two clusters (e.g., diffusion models + molecular design, or GNNs + causal inference) tend to have higher elite rates than papers squarely within one cluster. Bridges are valued.

4. **The AI x Science intersection** is still small but strategically important. Every major lab is hiring for it. The publication volume doesn't yet match the hiring demand, which means the field is talent-constrained, not idea-constrained.

## Methodology Notes

The full analysis used TF-IDF vectorization (8,611 features), UMAP for dimensionality reduction (n_neighbors=15, min_dist=0.1, metric=cosine), and KMeans (k=28, selected by silhouette score). Papers were scraped from OpenReview (ICLR) and the NeurIPS proceedings API.

All code and data are available — if you want to replicate or extend this to other venues (ICML, ACL, CVPR), reach out.

---

*This is part of my preparation for transitioning into frontier AI research. More at [kiryteo.github.io/blog](/blog/).*
