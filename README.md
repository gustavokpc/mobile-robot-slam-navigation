# Autonomous Mobile-Robot Navigation: SLAM, Planning, and Reinforcement Learning

Coursework repository for ENSTA Paris 4ROB1 / ROB201, centred on autonomous navigation in the [Place-Bot](https://github.com/emmanuel-battesti/place-bot) 2-D simulator. The primary project implements a LiDAR-and-odometry navigation loop: it estimates pose against an occupancy grid, updates the map, explores reactively, plans with A*, and follows the planned path. A separate bonus folder contains grid-world Q-learning experiments and recorded demonstrations.

The two tracks share an academic context but are intentionally independent:

- `tp_rob201/` is the Place-Bot mobile-robot navigation project.
- `TP_bonus_rob201/` is a separate multi-agent/grid-world reinforcement-learning exercise; it is not part of the simulator navigation control loop.

## Navigation pipeline

```text
Place-Bot LiDAR + odometry
          │
          ▼
Stochastic scan matching (`TinySlam.localise`)
          │
          ▼
Log-odds occupancy-grid update ──> OpenCV map display
          │
          ▼
Explore reactively ──> A* on dilated obstacles ──> Potential-field path follower
          │                                                        │
          └──────────────────── velocity command {forward, rotation} ┘
```

The default controller (`MyRobotSlam.control_tp4`) follows three phases:

1. **Explore** for 1,000 simulation iterations using LiDAR-based reactive obstacle avoidance while localising and mapping.
2. **Plan** an A* path from the corrected pose to the map origin. Occupied cells are dilated to leave a clearance margin.
3. **Follow** successive A* waypoints with a potential-field controller, returning to exploration after reaching the origin or if planning fails.

## Implemented components

| Area | Implementation |
| --- | --- |
| Simulation | `main.py` creates the Place-Bot simulator, `MyRobotSlam`, and a custom world with fixed walls and boxes. |
| Mapping | `OccupancyGrid` stores a bounded log-odds map, converts map/world coordinates, ray-traces free space, inserts LiDAR endpoints, clips values, and renders with OpenCV. |
| Localisation | `TinySlam` scores projected LiDAR endpoints in the map and performs a stochastic local search over the odometry-frame reference. |
| Reactive control | `reactive_obst_avoid` selects a free direction and reacts to frontal and lateral proximity. `potential_field_control` combines attractive and nearest-obstacle repulsive terms. |
| Global planning | `Planner.plan` converts poses to grid cells, dilates occupied cells, and runs 8-connected A* with Euclidean costs. |
| Path execution | `follow_path` advances through world-coordinate waypoints and delegates local steering to the potential-field controller. |
| RL bonus | The notebook contains tabular Q-learning and finite-horizon predictive-safety exercises; the vendored `gym-multigrid` project also includes Gymnasium environments and a PPO Capture-the-Flag script. |

## Repository layout

| Path | Purpose |
| --- | --- |
| `tp_rob201/main.py` | Primary simulator entry point |
| `tp_rob201/my_robot_slam.py` | Control state machine and module integration |
| `tp_rob201/tiny_slam.py`, `occupancy_grid.py` | Localisation and occupancy mapping |
| `tp_rob201/planner.py`, `control.py` | A* planning and robot controls |
| `tp_rob201/worlds/` | Custom simulated world geometry |
| `TP_bonus_rob201/` | Independent Q-learning notebook, demonstration videos, and multi-grid source |
| `TP_4ROB1.pdf` | Original course brief (French) |
| `INSTALL.md` | Upstream Place-Bot installation guidance |

## Requirements

The navigation project is Python-based and depends on the Place-Bot simulator. `requirements.txt` pins it to the upstream repository’s current `main` branch; that choice means reproducibility may change as upstream changes.

- Python 3.8+ is the original course baseline; use a Python version supported by the installed Place-Bot release.
- Place-Bot and its graphical/runtime dependencies (installed by `requirements.txt`).
- NumPy, OpenCV (`cv2`), and Matplotlib, which are imported by the navigation modules.
- A working OpenGL environment for the Place-Bot GUI. Ubuntu is the course’s recommended platform; see `INSTALL.md` for platform-specific notes.

The bonus environment has its own dependencies declared in `TP_bonus_rob201/gym-multigrid/pyproject.toml`, including Python 3.10+, Gymnasium, NumPy, Matplotlib, ImageIO, and optional development dependencies such as Stable-Baselines3 and PyTorch.

## Install and run the navigation project

From this repository’s root:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install -r requirements.txt
python3 tp_rob201/main.py
```

The simulator opens a GUI and the mapping module opens an OpenCV map window. The default `main.py` enables LiDAR noise and GPU shaders. On systems with graphics or sensor-rendering issues, change `use_shaders` in `tp_rob201/main.py` only after consulting the Place-Bot guidance in `INSTALL.md`.

## Reinforcement-learning bonus

See [the bonus README](TP_bonus_rob201/README.md) for the notebook and PPO experiment. The recorded outputs are intentionally kept with the exercise:

- [`agent_policy.mp4`](TP_bonus_rob201/agent_policy.mp4)
- [`policy_with_walls.mp4`](TP_bonus_rob201/policy_with_walls.mp4)
- [`on_the_fly_learning.mp4`](TP_bonus_rob201/on_the_fly_learning.mp4)

They demonstrate the grid-world exercise only; they are not videos of the Place-Bot SLAM/navigation pipeline.

## Limitations and engineering notes

- The world size, initial robot pose, map bounds, exploration duration, and goal at the map origin are hard-coded for the supplied scenario.
- The localisation search is stochastic and seeded only by the runtime environment; repeated runs can differ.
- This is a course implementation, not a safety-certified navigation stack: it has no formal collision guarantees, recovery planner, dynamic-obstacle model, or real-hardware interface.
- `OccupancyGrid.load` is a stub. `planner_todo.py` is a retained incomplete course scaffold; the active controller imports `planner.py`.
- Several source comments retain course `TODO` labels next to implemented work. They are preserved as historical coursework annotations, not treated as evidence that the pipeline was revalidated here.
- The project has no root-level automated test suite. The GUI-dependent simulator run was not executed as part of this documentation update.

## Academic background and attribution

This repository began from the ENSTA ROB201 / 4ROB1 course framework and the Place-Bot simulator, with original course materials credited to Emmanuel Battesti, David Filliat, Elena Vanneaux, and Thibault Toralba in `TP_4ROB1.pdf`. The navigation and bonus work in this copy was developed during the course; upstream frameworks and the vendored `gym-multigrid` project retain their own licenses and attribution. Do not present the upstream simulator or multi-grid framework as original work.
