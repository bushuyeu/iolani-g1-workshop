---
title: ʻIolani G1 Workshop
description: "Video-to-robot teleoperation for the Unitree G1 humanoid. Turn ordinary video into robot motion: no suits, no sensors, just a camera."
permalink: /
---

## Pipeline overview

```
Video (.mp4)  →  PromptHMR  →  GMR  →  GR00T-WBC  →  Unitree G1
                 (3D Pose)    (Retarget)  (Sim/Hardware)
```

| Stage | What it does | Output |
|-------|-------------|--------|
| **PromptHMR** | Extracts 3D human pose (SMPL-X) from video | `phmr_results.pkl` — 55 joints, world coords |
| **GMR** | Retargets human skeleton to robot joint space | `retarget_unitree_g1.pkl` — 29 DOF |
| **GR00T-WBC** | Replays motion in simulation or on real hardware | Robot stands and moves arms |

## Quickstart

```bash
git clone https://github.com/bushuyeu/iolani-g1-workshop && cd iolani-g1-workshop
bash setup.sh       # clones repos, creates venvs, installs deps (~30 min)
just check           # verify everything is set up

# Process a single video end-to-end
just pipeline data/videos/my_video.mp4

# Or batch-process all videos with monitoring
just pipeline-monitored-batch
```

## Architecture

```
iolani-g1-workshop/
├── setup.sh                 # One-command bootstrap
├── Justfile                 # All CLI recipes
├── .env                     # Sub-repo paths (auto-generated)
├── robotica/                # Monitoring & metrics package
│   ├── pipeline_monitor.py  # wandb logging wrapper
│   ├── metrics.py           # Artifact metric extraction
│   └── hf_upload.py         # HuggingFace Hub upload
├── data/videos/             # Input videos
├── results/                 # Shared pipeline outputs
├── PromptHMR/               # Stage 1 — 3D human mesh recovery
├── GMR/                     # Stage 2 — Joint retargeting
└── GR00T-WholeBodyControl/  # Stage 3 — Sim & hardware replay
```

Each sub-repo has its own Python virtual environment due to irreconcilable dependency trees (different PyTorch builds, Python versions). The meta-repo orchestrates everything through `just` recipes.

## Prerequisites

- Linux workstation with NVIDIA GPU (CUDA 12.6+)
- Docker + NVIDIA Container Toolkit (for GR00T-WBC)
- `gcloud` CLI with access to `gs://io-robotica` (or [SMPL-X credentials](https://smpl-x.is.tue.mpg.de) as fallback)
- ~50 GB free disk space

## Documentation

| Guide | Description |
|-------|-------------|
| [Reproduction guide](reproduction-guide.md) | Full walkthrough of the sim pipeline (Stages 1-3) |
| [Hardware deployment](hardware-deployment-guide.md) | Deploying on a physical Unitree G1 |
| [Command reference](commands.md) | All `just` recipes with descriptions |
| [Glossary](glossary.md) | Definitions of technical terms |
