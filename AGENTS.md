# AGENTS.md — Robotic Stack Repository Instructions

This file contains persistent repository instructions for Codex and compatible coding agents. It applies to the repository tree rooted here unless a more specific nested `AGENTS.md` or `AGENTS.override.md` applies.

## 1. Mission and Confirmed Direction

`robotic-stack` is a solo robotics engineering portfolio built as a unified monorepo. It progresses from a stationary arm to reusable embedded infrastructure, ROS 2 integration, a mobile manipulator, and multi-robot scenarios.

Confirmed project decisions:

- Initial robot: **Waveshare RoArm-M3-S**.
- Initial firmware platform: its onboard ESP32.
- Firmware baseline: **ESP-IDF + FreeRTOS**.
- Architecture goal: reusable project-owned logic with platform- and robot-specific adapters.

These are project decisions, not proof of electrical, protocol, timing, control, or safety properties. Hardware facts still require vendor documentation or measurement.

Project principle:

> Prefer reliable, measured, explainable robotics over unnecessary complexity.

## 2. Sources of Truth

Read only the documents relevant to the task:

- `README.md`: stable project overview and navigation.
- `docs/status/CURRENT.md`: current milestone, repository state, blockers, and next work.
- `docs/plans/robotic-stack-plan.md`: long-term roadmap and planned architecture.
- `docs/development/GIT_WORKFLOW.md`: detailed branch, commit, PR, CI, and release process.
- `docs/architecture/`: established architecture, when present.
- `docs/decisions/`: significant decisions and rationale, when present.
- Code, configuration, tests, experiment records, and Git history: evidence of implementation and validation.

Do not silently replace established decisions with generic best practices. If documents disagree with each other or with the working tree, identify the inconsistency and update the authoritative document when that is in scope.

## 3. Codex Responsibilities and Handoff

The user is the project owner and final decision-maker.

Codex primarily inspects, implements, tests, documents, and reports repository changes. The repository is the durable handoff point for project work.

This file governs Codex and other coding agents. ChatGPT Project-specific behavior belongs in `docs/development/CHATGPT_PROJECT_INSTRUCTIONS.md`. Do not assume access to a separate chat transcript. Record decisions that must persist in repository documentation.

Before implementing a non-trivial task, make sure its objective, constraints, acceptance criteria, validation method, and hardware requirements are stated in the request or a tracked plan.

## 4. Repository Boundaries

Use these conceptual ownership boundaries:

```text
firmware/       MCU, RTOS, hardware-near software, portable embedded logic
ros2_ws/        ROS 2 and Linux/Jetson robotics software
robots/         physical-robot configuration and calibration
simulation/     simulation assets and integration
tools/          reusable development and experiment utilities
experiments/    experiment definitions, selected data, and results
scenarios/      integrated application scenarios
docs/           plans, architecture, decisions, guides, and reports
dependencies/   pinned external and vendor dependency definitions
media/          selected portfolio media
```

Introduce directories only when they support real work. Avoid duplicate ownership of the same concept.

### Firmware boundaries

- Keep reusable `core/`, `control/`, and `estimation/` logic independent of ESP-IDF and FreeRTOS headers.
- Keep the OS abstraction in `firmware/osal/` narrow.
- Keep ESP-IDF integration under `firmware/platforms/esp32_idf/`.
- Keep Waveshare RoArm-M3-S composition, mapping, and configuration in its platform subtree.
- Keep generic smart-servo protocol and transport logic outside the robot-specific composition layer.
- Prefer deterministic, explicit, testable logic over RTOS-heavy designs.

### Communication boundaries

Keep these concepts separate where practical:

```text
domain/message model
protocol serialization and parsing
transport
robot interface
ROS 2 or application logic
```

Handle malformed, partial, stale, and missing communication at the appropriate layer.

### Linux and ROS 2 boundaries

Use the Linux/Jetson side for ROS 2, modeling, planning, perception, navigation, orchestration, logging, and visualization. Keep hardware access behind explicit interfaces and do not couple high-level logic directly to vendor protocol details.

## 5. Development Workflow

Follow `docs/development/GIT_WORKFLOW.md` for the full process.

Before editing:

1. inspect `git status --short --branch`,
2. read nearby code and relevant documentation,
3. identify the smallest coherent change,
4. preserve unrelated user work.

For normal work:

- `main` is the stable milestone branch.
- `dev` is the integration branch.
- Use a short-lived task branch from current `dev` when the environment permits.
- Target normal PRs to `dev`.
- Use lowercase kebab-case branch names such as `feat/roarm-transport`, `docs/current-status`, or `fix/watchdog-timeout`.
- Prefer concise Conventional Commit messages when commits are requested.

Never do the following without explicit user authorization:

- develop directly on `main` or `dev` when task branches are available,
- merge a task branch into `dev`,
- merge `dev` into `main`,
- create releases or tags,
- force-push or rewrite shared history,
- delete protected branches,
- discard, reset, clean, stash, or overwrite unrelated user changes.

Do not expand a task into an unsolicited reorganization, framework replacement, dependency upgrade, API redesign, or broad refactor. Report important adjacent work as a follow-up.

## 6. Hardware Facts and Safety

Do not invent or silently hard-code unverified properties, including:

- joint limits,
- motor direction,
- torque, current, payload, or encoder capability,
- communication or control frequency,
- latency, backlash, or repeatability,
- safe velocity or acceleration,
- feedback, protocol, or fault behavior.

Label information as one of:

- project decision,
- vendor specification,
- assumption,
- simulated result,
- bench result,
- physically measured result.

Keep unknown values configurable and replaceable.

For changes capable of physical motion:

- begin with supervised, low-speed, limited-scope tests,
- prefer one joint or component before coordinated motion,
- preserve limits, watchdogs, faults, and a clear stop or disable path,
- do not weaken safety checks to make a test pass,
- do not execute robot motion autonomously unless the user explicitly requests it and supervised hardware access is available.

Treat Waveshare and other third-party code as external dependencies. Preserve provenance and licenses; prefer pinned dependencies, adapters, or documented forks over copied vendor trees.

## 7. Testing and Validation

Test at the correct layer:

- unit tests for pure logic,
- parser and serialization tests for protocol code,
- host-side tests for portable firmware logic,
- target builds/tests for ESP-IDF integration,
- ROS 2 build/tests for ROS packages,
- simulation for integrated robot behavior,
- supervised bench tests for electrical and communication behavior,
- repeatable experiments for physical performance claims.

Use repository-provided commands. Do not invent a command and claim it passed. If validation cannot run because hardware, dependencies, permissions, or environment support are unavailable, say so explicitly.

Distinguish clearly between:

```text
unit-tested
built successfully
simulated
bench-tested
physically tested on the robot
```

CI does not replace physical validation.

## 8. Configuration, Experiments, and Documentation

- Put robot-specific settings such as limits, gains, transforms, IDs, and communication settings in reviewable configuration files.
- Preserve enough context to reproduce calibration changes.
- Update documentation when behavior, architecture, public interfaces, setup, or configuration changes materially.
- Do not describe planned functionality as implemented or simulated work as hardware-validated.
- Update `docs/status/CURRENT.md` only when a task materially changes the current phase, verified state, blocker, or next work.

Create or update an ADR for significant, durable choices such as protocol architecture, transport, RTOS/toolchain, controller baseline, estimator, calibration architecture, or vendor-fork strategy. Do not create ADRs for trivial implementation details.

Experiments should record the question, commit, configuration, procedure, inputs, measurements, results, and conclusion. Use quantitative metrics when appropriate. Avoid committing large raw logs to normal Git history.

## 9. Dependencies, Secrets, and Generated Files

- Do not upgrade major toolchains or dependencies casually. Treat ROS, ESP-IDF, FreeRTOS, compiler, SDK, and major library changes as explicit tasks.
- Never commit credentials, tokens, passwords, private keys, Wi-Fi secrets, private certificates, or secret-bearing `.env` files.
- Keep ordinary generated output ignored, including build, install, log, cache, and compiled-intermediate directories.
- Commit selected plots or reports only when they are deliberate engineering evidence.

If a secret appears in tracked or staged changes, stop and report it.

## 10. Definition of Done

Apply this proportionally to the task. Relevant completion evidence includes:

- the requested behavior or documentation exists,
- inputs, outputs, configuration, and failure cases are documented,
- meaningful tests or checks pass,
- performance claims have measurements,
- results are reproducible,
- hardware validation status is explicit,
- the implementation is understandable and reviewable.

Before finishing:

1. inspect the relevant diff,
2. run `git diff --check`,
3. inspect `git status --short --branch`,
4. check for generated files, secrets, debugging remnants, unrelated formatting, and stale documentation.

Final reports must state:

1. what changed,
2. exact validation and outcomes,
3. whether physical hardware was used,
4. branch and commit state when relevant,
5. meaningful limitations or follow-ups.

Never claim a test or hardware validation that was not performed.

## 11. Nested Instructions

Add nested `AGENTS.md` files only when a subsystem has real, distinct rules that would otherwise burden unrelated work. Do not create them merely because a directory exists.
