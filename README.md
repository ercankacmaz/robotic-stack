# robotic-stack

`robotic-stack` is a robotics portfolio monorepo for building and validating a reusable robotics architecture from embedded control up to integrated robotic applications.

The planned development path is:

1. stationary robot arm bring-up,
2. reusable embedded control and communication infrastructure,
3. ROS 2, kinematics, planning, and calibration integration,
4. a mobile manipulator using the same architecture,
5. multi-robot collaboration and final scenario validation.

The goal is not to collect disconnected demos. The goal is to show reliable, measured, explainable robotics across embedded systems, control, communication, estimation, ROS 2, perception, planning, navigation, safety, and quantitative validation.

## Current Status

This repository is at the initial bootstrap stage.

Current contents:

- repository instructions in `AGENTS.md`,
- development workflow in `docs/development/GIT_WORKFLOW.md`,
- portfolio roadmap in `docs/plans/robotic-stack-plan.md`,
- initial firmware skeleton in `firmware/`.

Implementation has not started yet beyond repository bootstrap and initial firmware structure.

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

Detailed workflow rules are documented in `docs/development/GIT_WORKFLOW.md`.

## Key Documents

- `AGENTS.md`
- `docs/development/GIT_WORKFLOW.md`
- `docs/plans/robotic-stack-plan.md`

These documents define the repository constraints, workflow, and project direction.
