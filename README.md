# Teaching a robot to walk — PPO on BipedalWalker

**English** · [Español](README.es.md)

> **Academic project** · MSc in Data Science and Artificial Intelligence (MCDIA) · Afi Escuela · 2025-26
> Module: *Reinforcement Learning* · Group assignment with Said Daniel Huarita Mollo

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Stable-Baselines3](https://img.shields.io/badge/Stable--Baselines3-PPO-4B8BBE)
![Gymnasium](https://img.shields.io/badge/Gymnasium-BipedalWalker--v3-0081A5)

<p align="center">
  <img src="images/agent_3_advanced.gif" width="480" alt="Trained agent walking">
  <br><em>Best agent: average reward of 300.5 ± 1.0, above the 300-point threshold for solving the environment</em>
</p>

## Goal

Train a simulated two-legged robot to walk across rough terrain as efficiently as possible using **Proximal Policy Optimization (PPO)**, and study how the main hyperparameters affect learning.

## Environment

[BipedalWalker-v3](https://gymnasium.farama.org/environments/box2d/bipedal_walker/) (Gymnasium, Box2D): **24 observations** (hull angle and velocity, joint positions and speeds, leg contact with the ground, 10 lidar readings) and **4 continuous actions** (torque on hips and knees). The robot earns reward for moving forward, pays a small cost for applying torque and loses 100 points if it falls. The environment counts as **solved at 300 points**.

## Experiments

Four agents, each changing one or more things compared with the previous one, all trained with Stable-Baselines3 and evaluated over 100 episodes in an independent environment:

| Agent | Parallel envs | Network (`net_arch`) | Entropy coef. | Timesteps | Mean reward (train) | Mean reward (independent eval) |
|---|---|---|---|---|---|---|
| 1. Basic | 1 | [64, 64] | 0.0 | 100k | 189.41 ± 95.68 | 213.48 ± 78.10 |
| 2. Medium | 4 | [128, 128] | 0.0 | 100k | -46.12 ± 73.91 | -48.76 ± 70.83 |
| **3. Advanced** | **8** | **[128, 128]** | **1e-4** | **400k** | **297.64 ± 19.57** | **300.50 ± 1.04** |
| 4. Complex | 8 | [256, 256] | 1e-4 | 400k | 271.49 ± 1.56 | 271.51 ± 1.69 |

| 1. Basic | 2. Medium | 3. Advanced | 4. Complex |
|---|---|---|---|
| ![](images/agent_1_basic.gif) | ![](images/agent_2_medium.gif) | ![](images/agent_3_advanced.gif) | ![](images/agent_4_complex.gif) |

## Key findings

- **Exploration was the key.** Adding a small entropy coefficient (`ent_coef=1e-4`) stopped the agent from settling early on poor strategies, such as standing still or falling, and let it discover that walking pays off much more.
- **Consistency improves as the strategy gets better.** The standard deviation drops from ±96 (erratic basic agent) to ±1 (advanced agent): the robot walks almost the same way in every episode.
- **A bigger network is not automatically better.** The [256, 256] agent is more consistent but scores lower than [128, 128]. With the same 400k timesteps, the larger network has more parameters to fit and had not finished converging.

![Mean episode reward during training](images/rollout_ep_rew_mean.png)

The notebook also analyses the TensorBoard curves for mean episode length and training speed (FPS).

## Repository structure

```
├── ppo_bipedal_walker.ipynb   # Training, evaluation and analysis (in Spanish)
├── videos/                    # Full-length videos of each agent (2,000 steps)
├── images/                    # GIFs and TensorBoard curves used in this README
└── requirements.txt
```

## How to run

```bash
python -m venv venv
venv\Scripts\activate          # Windows  (source venv/bin/activate on macOS/Linux)
pip install swig
pip install -r requirements.txt
jupyter notebook ppo_bipedal_walker.ipynb
```

Training runs on CPU; the 400k-step agents take about 12 minutes each.

---

*Group assignment completed with **Said Daniel Huarita Mollo** as part of the assessment for the MSc in Data Science and Artificial Intelligence at Afi Escuela. The notebook template was provided by the course. Grade not included.*
