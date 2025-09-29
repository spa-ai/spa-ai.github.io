# SPA: Self-Play with World Model for LLM Agents

<p align="center">
  <a href="#homepage"><img src="https://img.shields.io/badge/Homepage-orange?style=for-the-badge"></a>
  <a href="paper.pdf"><img src="https://img.shields.io/badge/Paper-red?style=for-the-badge"></a>
  <a href="#post"><img src="https://img.shields.io/badge/Post-green?style=for-the-badge"></a>
  <a href="#experiment-log"><img src="https://img.shields.io/badge/Experiment%20Log-purple?style=for-the-badge"></a>
  <a href="https://github.com/spa-ai/SPA"><img src="https://img.shields.io/badge/GitHub-000000?style=for-the-badge&logo=github"></a>
</p>

---

# Homepage

## Introduction

Large Language Models (LLMs) as agents face challenges in **out-of-distribution (OOD)** environments. While vanilla RL training may increase **Pass@1**, it often causes **Pass@k** to collapse, exposing brittle exploration and limited generalization.

**SPA (Self-Play Agent)** addresses this by equipping LLM agents with an **internal world model** via **self-play supervised finetuning (SFT)**. By explicitly modeling **states** and **transitions**, SPA provides structured reasoning aligned with environment dynamics. This initialization enables more efficient and stable **PPO training**.

---

## Framework Overview

SPA decomposes world modeling into two key components:

1. **State Estimation** – structured representations of observations (e.g., spatial coordinates in Sokoban) that reduce perplexity and improve grounding.
2. **Transition Modeling** – predicting future states conditioned on current state and actions.

The training pipeline follows three stages:

* **Data Generation** – Collect self-play trajectories with `<observation>` and `<prediction>`.
* **SFT (World Model Training)** – Train the model to internalize environment dynamics.
* **PPO Optimization** – Fine-tune with reinforcement learning for performance.

<p align="center">
  <img src="assets/spa_wm_overview.png" alt="SPA world-model pipeline" width="950"/>
</p>

---

## Key Results

Experiments across diverse environments highlight SPA’s effectiveness:

* **Sokoban (6×6)** – Pass@1: **25.6% → 59.8%**, Pass@8: **34.0% → 69.5%**
* **FrozenLake (4×4/6×6)** – Pass@1: **22.1% → 70.9%**, Pass@8: **30.7% → 75.0%**
* **Sudoku (4×4 puzzles)** – Pass@1: **0.0% → 59.6%**, Pass@8: **11.3% → 94.9%**

Notably, SPA-trained **small models** (e.g., Qwen2.5-0.5B, LLaMA-3.2-1B) can surpass larger baselines like GPT-OSS-20B on text games.

---

## Example World Model Trace

```text
<think>
  <observation>
  ######
  #___O#
  #__X_#
  ###P_#
  ###__#
  ######
  Player (P) at (3,3); box (X) at (2,3); goal (O) at (1,4).
  </observation>
  <prediction>
  ######
  #___O#
  #____#
  ###X_#
  ###P_#
  ######
  </prediction>
</think>
<answer>Up</answer>
```

This explicit **observation → prediction → action** format grounds decisions in true environment dynamics.

---

## Findings & Analysis

* **Transition modeling is essential**: Masking transition spans in SFT eliminates SPA’s gains.
* **Ground-truth matters**: Randomizing coordinates while keeping format collapses performance.
* **Policy-aware exploration**: Self-play data from a trained policy yields higher-quality supervision than random rollouts.
* **Longer SFT helps**: More SFT epochs consistently improve PPO performance and shorten trajectories.
* **Generalization**: Easy→Hard transfer works within environment families (e.g., FrozenLake 4×4 → 6×6), but cross-game transfer remains difficult.

---

## Conclusion

SPA demonstrates a **minimal yet powerful recipe** for improving RL agents:

1. Build a world model via self-play SFT.
2. Initialize PPO with this world model.
3. Achieve robust improvements in grounding, reasoning, and generalization across OOD environments.

---

## Citation

```bibtex
@inproceedings{spa2026,
  title={Internalizing World Models via Self-Play Finetuning for Agentic RL},
  author={Anonymous},
  booktitle={International Conference on Learning Representations (ICLR)},
  year={2026},
  note={Under review}
}
```
