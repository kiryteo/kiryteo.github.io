---
title: "Projects"
layout: "single"
url: "/projects/"
summary: "Selected projects"
ShowToc: false
ShowBreadCrumbs: false
---

## Current

### CellGraph-FM
*Foundation model for 3D cell morphology*

A hybrid foundation model + graph reasoning system for cell biology. Self-supervised pretraining (MAE/DINO ViT) on Allen Institute's 3D cell images to learn per-cell representations, followed by graph neural networks that reason over cell-cell interactions, state transitions, and organelle co-localization patterns.

**Stack**: PyTorch, MONAI, PyG, OME-Zarr | **Compute**: 4x A100-SXM4-80GB | **Status**: Active development

---

### CellGraph
*Knowledge graph for scientific teams* · [GitHub](https://github.com/kiryteo/cellgraph)

A production knowledge graph serving Allen Institute's ~80-person Cell Science division. Connects people, papers, skills, and research frontiers. Features include blind spot detection (research areas nobody on the team covers), emerging frontier tracking ("Pulse" system), and semantic search over publications using SPECTER2 embeddings with LLM-based re-ranking.

**Stack**: Neo4j, SPECTER2, LLM re-ranking, Python | **Users**: ~80 | **Status**: Production

---

## Tools & Libraries

### paper2skills
*Research paper to skill file extractor* · [GitHub](https://github.com/kiryteo/paper2skills) · [PyPI](https://pypi.org/project/paper2skills/)

A pip-installable CLI that extracts structured skill files from research papers. Supports 4 LLM providers, configurable extraction pipelines, and batch processing. 893 tests.

**Stack**: Python, Click, multiple LLM APIs | **Status**: Published on PyPI

---

### cvlab-peer
*Computer vision research peer*

An AI research peer for computer vision work. 24 MCP tools covering the full research workflow: image inspection, rendering, segmentation training (MONAI), SSL pretraining (MAE/DINO), embedding analysis, and evidence pack generation. 300 tests.

**Stack**: Python, MONAI, PyTorch, MCP | **Status**: Active development

---

## Past Work

### Tropical Cyclone Detection & Tracking
*ESoWC 2020 & 2021 — collaboration with ECMWF* · [2020](https://github.com/esowc/TC-Tracking) · [2021](https://github.com/esowc/climetlab)

Deep learning methods for detecting and tracking tropical cyclones in reanalysis data. Extended into a CliMetLab plugin for the 2021 iteration.

---

### nERdy — Network Analysis of ER Dynamics
*M.Sc. Thesis Work* · [Paper](https://www.biorxiv.org/content/10.1101/2024.02.20.581259v1.abstract)

Network-based methods for quantifying endoplasmic reticulum structural dynamics from super-resolution microscopy. Part of the NanoscopyAI collaboration between SFU and UBC.

---

### Strong Lensing Parameter Estimation
*Collaboration with DeepSkies Lab* · [Paper](https://arxiv.org/abs/2211.05836)

Simulation-based inference for estimating strong gravitational lensing parameters from ground-based imaging data. NeurIPS 2022 ML4PS Workshop.

---

### Blood Cell Classification
*CNRS CINAM, Marseille* · [Paper](https://www.nature.com/articles/s41598-023-27718-w)

Convolutional and recurrent neural networks for classifying blood cell dynamics in sickle cell disease patients from microfluidic blood flow recordings.

---

### Radio Galaxy Classification
*Collaboration with IUCAA* · [Paper](https://doi.org/10.1093/mnras/stab3144)

Data-efficient classification of radio galaxy morphologies, published in Monthly Notices of the Royal Astronomical Society.

---

### ROOT Package Manager
*Google Summer of Code 2018 — CERN-HSF* · [GSoC](https://summerofcode.withgoogle.com/archive/2018/projects/6244789099954176/)

Built a package manager for the ROOT data analysis framework at CERN.
