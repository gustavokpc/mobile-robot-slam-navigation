# Reinforcement-Learning Bonus Experiments

This directory is an independent extension to the Place-Bot navigation coursework. It contains grid-world reinforcement-learning exercises, not a policy that controls `tp_rob201/MyRobotSlam`.

## Contents

| Path | Description |
| --- | --- |
| `TP_plannification_Qlearning.ipynb` | Tabular Q-learning exercises covering offline policy learning and a model-based finite-horizon safety experiment. |
| `agent_policy.mp4` | Recorded notebook policy rollout. |
| `policy_with_walls.mp4` | Recorded rollout with walls. |
| `on_the_fly_learning.mp4` | Recorded on-the-fly-learning rollout. |
| `gym-multigrid/` | Vendored Gymnasium multi-agent grid-world project used by the exercises. |

## Notebook workflow

Open the notebook from this directory so its relative `gym-multigrid` path resolves:

```bash
cd TP_bonus_rob201
jupyter notebook TP_plannification_Qlearning.ipynb
```

The first notebook cell installs/imports Gymnasium, Matplotlib, Pandas, and ImageIO. Review it before running: package installation and video rendering are interactive notebook actions. The notebook preserves incomplete instructional cells marked `TODO`; use the existing recorded videos as demonstrations of the saved outputs, not as proof that every exercise cell is complete.

## PPO Capture-the-Flag experiment

`gym-multigrid/scripts/main_mvn_ctf_rl.py` defines a Stable-Baselines3 PPO experiment for a 2-vs-2 Capture-the-Flag environment. Its project metadata uses Poetry:

```bash
cd gym-multigrid
poetry install
mkdir -p out/logs/ctf out/models out/animations
poetry run python scripts/main_mvn_ctf_rl.py
```

The script trains for 1,000,000 timesteps unless an existing model is found, may use CUDA if available, and writes models/logs/animations to `out/`. It has not been run or re-trained in this portfolio update.

## Attribution

`gym-multigrid` is retained as a vendored third-party project. Its own README identifies the Tran Research Group grid-world environment and its upstream lineage; preserve its license and citation information when redistributing or extending it.
