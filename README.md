# robotic-stack

`robotic-stack` is a robotics portfolio monorepo for building and validating a reusable robotics architecture from embedded control up to integrated robotic applications.

The portfolio is planned to progress in stages:

1. stationary robot arm bring-up,
2. reusable embedded control and communication infrastructure,
3. ROS 2, kinematics, planning, and calibration integration,
4. a mobile manipulator using the same architecture,
5. multi-robot collaboration and final scenario validation.

The engineering goal is not to collect disconnected demos. The goal is to show reliable, measured, explainable robotics across:

- embedded systems,
- feedback control,
- actuator and sensor interfaces,
- communication,
- estimation,
- ROS 2 integration,
- kinematics and planning,
- perception and calibration,
- navigation,
- safety and fault handling,
- experiments and quantitative validation,
- technical documentation.

## Current Status

This repository is at the initial bootstrap stage.

Current contents:

- repository instructions in `AGENTS.md`,
- Git workflow in `GIT_WORKFLOW.md`,
- portfolio roadmap in `robotics_portfolio_9_month_plan_README.md`.

Implementation work has not started yet.

## Planned Repository Structure

The intended monorepo structure is:

```text
robotic-stack/
├── docs/
├── firmware/
├── ros2_ws/
├── robots/
├── simulation/
├── tools/
├── experiments/
├── scenarios/
├── dependencies/
└── media/
```

This structure will be introduced incrementally. Directories should only be added when they support real work.

## Workflow

This repository follows a branch-based workflow:

- `main` is the stable milestone branch,
- `dev` is the integration branch,
- normal work happens on short-lived task branches,
- task branches open pull requests into `dev`,
- milestone releases merge `dev` into `main`.

Detailed workflow rules are documented in `GIT_WORKFLOW.md`.

## Source Documents

- `AGENTS.md`
- `GIT_WORKFLOW.md`
- `robotics_portfolio_9_month_plan_README.md`

These documents define the current repository constraints, workflow, and project direction.
