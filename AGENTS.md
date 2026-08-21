# AGENTS.md — Robotics Portfolio Repository Instructions

This file contains persistent instructions for Codex and other compatible coding agents working in this repository.

It applies to the repository tree rooted at this file unless a more specific nested `AGENTS.md` or `AGENTS.override.md` provides instructions for a subdirectory.

---

# 1. Project Mission

This is a solo robotics engineering portfolio built as a unified monorepo.

The project progresses from:

1. a stationary robot arm,
2. reusable embedded and robotics infrastructure,
3. ROS 2 / planning / perception integration,
4. a mobile manipulator,
5. autonomous navigation and sensor fusion,
6. multi-robot collaboration,
7. final quantitative portfolio scenarios.

The goal is not merely to make inexpensive robots move. The repository should demonstrate professional capability across:

- embedded systems,
- real-time control,
- actuator and sensor interfaces,
- communication,
- estimation,
- ROS 2,
- kinematics,
- planning,
- perception,
- navigation,
- safety/fault handling,
- experiments,
- quantitative validation,
- documentation,
- system integration.

The project philosophy is:

> Prefer reliable, measured, explainable robotics over unnecessary complexity.

---

# 2. Source of Truth and Project Context

Before making architectural or workflow changes, inspect the relevant repository documentation.

Important documents may include:

```text
README.md
docs/development/GIT_WORKFLOW.md
docs/architecture/
docs/decisions/
docs/plans/
```

Do not silently replace established project decisions with generic best practices.

If code and documentation disagree, identify the inconsistency in the final report and update documentation when that is clearly part of the requested task.

---

# 3. Repository Architecture

The intended monorepo boundaries are conceptually:

```text
firmware/       MCU / RTOS / hardware-near software
ros2_ws/        ROS 2 and Linux/Jetson robotics software
robots/         physical-robot-specific configuration/calibration
simulation/     simulation-specific assets and integration
tools/          reusable developer/experiment utilities
experiments/    experiment definitions, selected data, results
scenarios/      integrated application scenarios when present
docs/           architecture, decisions, reports, guides
dependencies/   pinned external/vendor dependency definitions
media/          selected portfolio media
```

Respect these boundaries unless the task explicitly changes the architecture.

Avoid creating duplicate ownership of the same concept across unrelated directories.

---

# 4. Git Branch Model

The permanent branches are:

```text
main
dev
```

Their roles are:

- `main`: stable milestone/release branch.
- `dev`: integrated development branch for the next milestone.

Normal feature/fix work targets `dev` through a pull request.

Milestone release work merges `dev` into `main` through a release PR.

Do not treat `main` as the daily development branch.

---

# 5. Git Safety Rules

These rules are mandatory unless a direct user instruction for the current task overrides them.

## 5.1 Never silently modify protected branches

Do not intentionally perform normal development directly on:

```text
main
dev
```

If the execution environment already placed the task on a feature/task branch, continue using that branch.

If branch creation is allowed and the repository is currently on `main` or `dev`, create an appropriately named task branch before making normal code changes.

If the environment explicitly forbids branch creation or controls branches externally, obey the environment and do not attempt to bypass it. Still follow the remaining workflow rules and clearly report the branch situation.

## 5.2 Do not merge protected branches autonomously

Unless the user explicitly requests it, do not:

- merge a task branch into `dev`,
- merge `dev` into `main`,
- create a release tag,
- publish a release,
- force-push,
- rewrite shared history,
- delete protected branches.

Prepare changes so they are ready for PR/review instead.

## 5.3 Never force-push by default

Do not use:

```text
git push --force
git push -f
```

Do not rewrite published history unless the user explicitly asks and the consequences are understood.

## 5.4 Do not amend unrelated existing commits

Prefer new commits for agent-authored changes.

Do not alter historical commits merely to make history prettier.

## 5.5 Protect unrelated user work

Before editing:

```bash
git status --short --branch
```

Inspect existing modifications.

Do not overwrite, discard, reset, clean, stash, or revert unrelated user changes unless explicitly requested.

Never use destructive Git commands such as `git reset --hard` or `git clean -fd` merely to obtain a clean workspace.

---

# 6. Task Branch Naming

When branch creation is appropriate and permitted, use:

```text
feat/<short-topic>
fix/<short-topic>
test/<short-topic>
docs/<short-topic>
ci/<short-topic>
refactor/<short-topic>
chore/<short-topic>
experiment/<short-topic>
hotfix/<short-topic>
```

Use lowercase kebab-case.

Examples:

```text
feat/roarm-serial-transport
feat/mock-robot-transport
fix/watchdog-timeout
ci/ros-build
experiment/joint1-step-response
```

Normal branches should start from current `dev` when the environment allows this safely.

A true released-state hotfix starts from `main`.

Do not create permanent personal branches such as `my-dev` or `codex-work`.

---

# 7. Commit Rules

When the task/environment expects Codex to commit changes, use concise Conventional-Commit-style messages.

Format:

```text
<type>(optional-scope): <imperative summary>
```

Recommended types:

```text
feat
fix
test
docs
ci
refactor
chore
perf
build
```

Examples:

```text
feat(protocol): add mock RoArm transport
fix(firmware): reject invalid joint limit
ci: run Python tests on pull requests
docs: document communication architecture
```

Rules:

- Keep a commit logically coherent.
- Do not mix unrelated cleanup with the requested change.
- Explain non-obvious rationale in the commit body when useful.
- Never include secrets or credentials in commit messages.
- Do not create meaningless messages such as `fix`, `update`, `final`, or `changes`.

If the task environment creates commits automatically, do not create redundant commits solely to satisfy this file.

---

# 8. Pull Request Rules

Normal development PR target:

```text
feature/fix/docs/ci/refactor/... → dev
```

Release PR target:

```text
dev → main
```

Hotfix PR target:

```text
hotfix/... → main
```

After a hotfix, ensure the fix is also propagated to `dev` when requested or when handling the full hotfix workflow.

For ordinary task branches, the preferred merge strategy is **Squash and Merge**.

For milestone `dev` → `main` releases, prefer a normal merge commit so the integrated milestone history remains visible.

Do not merge a PR yourself unless the user explicitly requests the merge or the task environment explicitly defines merge as part of the task.

---

# 9. PR Description Expectations

When asked to prepare a PR description, include:

```markdown
## What

## Why

## Changes

## Validation

## Evidence

## Risks / Limitations

## Related
```

For robotics changes, distinguish clearly between:

```text
unit-tested
built successfully
simulated
bench-tested
physically tested on robot
```

Never describe simulation or mocks as real hardware validation.

For control, timing, communication, perception, or calibration work, include quantitative evidence when it exists.

---

# 10. Development Process

For a normal implementation task:

1. Inspect repository state and relevant instructions.
2. Read nearby code before designing replacements.
3. Identify the smallest coherent change that solves the task.
4. Preserve existing architectural boundaries unless the task requires changing them.
5. Implement the change.
6. Add or update tests where meaningful.
7. Update affected documentation/configuration when needed.
8. Run relevant validation.
9. Inspect the final diff.
10. Check Git status.
11. Commit if the task/environment expects a commit.
12. Report exactly what changed and what was validated.

---

# 11. Scope Discipline

Do not expand a task simply because adjacent improvements are possible.

Avoid unsolicited large-scale:

- directory reorganizations,
- framework replacements,
- dependency upgrades,
- formatting of unrelated files,
- API redesigns,
- broad refactors.

If an adjacent issue is important but outside scope, mention it as follow-up rather than silently implementing it.

Prefer small reviewable changes.

---

# 12. Architecture Decision Policy

Create or update an ADR/decision record when a task makes a meaningful decision that is likely to matter later.

Examples:

- monorepo vs multi-repo,
- protocol architecture,
- vendor fork vs wrapper,
- ROS distribution,
- transport choice,
- controller baseline,
- estimator selection,
- calibration architecture.

Do not create ADRs for trivial implementation details.

Decision records should explain:

- context,
- decision,
- alternatives,
- consequences,
- condition for revisiting the decision.

---

# 13. Vendor and Third-Party Code

Treat Waveshare and other third-party code as external dependencies by default.

Prefer:

- pinned dependencies,
- `.repos` files,
- package-manager dependencies,
- wrappers/adapters,
- project-owned interfaces.

Do not copy large external repositories into project-owned directories and then modify them without a documented reason.

Do not imply third-party code was authored by this project.

If an upstream change is required, prefer a documented fork or patch strategy and preserve provenance/license information.

---

# 14. Hardware Assumption Rule

Do not invent or hard-code unverified physical properties merely to complete code.

Examples requiring real documentation or measurement include:

- joint limits,
- servo torque/current capabilities,
- communication frequency,
- control-loop frequency,
- encoder resolution,
- latency,
- backlash,
- payload,
- motor direction,
- maximum safe velocity,
- maximum safe acceleration.

If hardware information is not yet known:

- use explicit configuration placeholders,
- use documented vendor values only when clearly identified as vendor specifications,
- mark assumptions,
- keep code structured so measured values can replace assumptions later.

Do not present an assumed value as a measured value.

---

# 15. Hardware Safety Rules

Changes capable of causing physical motion require conservative handling.

Be especially careful with:

- actuator enable/disable,
- joint limits,
- motor direction,
- velocity/acceleration limits,
- trajectory generation,
- watchdogs,
- fault reset behavior,
- emergency-stop logic,
- firmware flashing,
- autonomous motion.

When preparing hardware tests:

- start with low-speed / limited-scope tests,
- prefer one joint/component before multi-joint motion when appropriate,
- preserve joint-limit checks,
- preserve watchdog/fault handling,
- provide a clear stop/disable path,
- do not remove safety checks merely to make a test pass.

Do not execute potentially unsafe physical motion autonomously unless the user explicitly requested the hardware action and the available environment actually supports supervised execution.

---

# 16. Firmware Rules

Firmware is intended for deterministic and hardware-near responsibilities such as:

- actuator control,
- encoder/joint-state acquisition,
- timing,
- watchdogs,
- limits,
- faults,
- timestamped telemetry,
- low-level communication,
- basic filtering/control where appropriate.

Keep reusable logic separate from platform-specific code.

Prefer interfaces that can later be reused by the mobile manipulator.

Do not introduce board-specific dependencies into reusable core modules without a clear abstraction boundary.

---

# 17. ROS 2 / Linux Rules

The Linux/Jetson side is intended for higher-level functions such as:

- ROS 2 integration,
- robot model/state publication,
- MoveIt,
- perception,
- sensor fusion where appropriate,
- navigation,
- high-level planning/control,
- orchestration,
- logging,
- visualization.

Keep hardware access behind explicit interfaces/adapters where practical.

Avoid coupling high-level application logic directly to vendor-specific protocol details.

---

# 18. Communication Architecture Rules

Keep these concepts separate where practical:

```text
message/domain model
protocol serialization/parsing
transport
robot interface
higher-level ROS/application logic
```

For example:

```text
RobotTransport
    ├── MockTransport
    └── SerialTransport
```

This enables development and testing before hardware is available and prevents protocol details from leaking through the entire software stack.

Add error handling for malformed, partial, stale, or missing communication where relevant.

---

# 19. Testing Philosophy

Tests should target the correct layer.

Use, where appropriate:

- unit tests for pure logic,
- parser/serialization tests for protocol code,
- host-side tests for reusable firmware logic,
- ROS 2 build/tests for ROS packages,
- simulation for robot integration,
- bench/hardware tests for physical behavior,
- repeatable experiments for quantitative claims.

Do not add elaborate tests that provide little confidence merely to increase test count.

Do not remove or weaken tests just to make CI green without understanding the failure.

---

# 20. Required Validation Behavior

After making changes, run the relevant existing checks documented by the repository.

Look for project-provided commands in files such as:

```text
README.md
Makefile
CMakeLists.txt
pyproject.toml
package.xml
.github/workflows/
scripts/
AGENTS.md
```

Do not invent a validation command and claim success if the project does not support it.

If a relevant test cannot run because hardware, dependencies, permissions, or environment support are missing, state this explicitly.

Examples:

```text
PASS: Python unit tests
PASS: ROS package build
NOT RUN: physical joint motion test — robot unavailable
NOT RUN: firmware flash — no hardware access
```

---

# 21. CI Policy

CI should remain incremental and understandable.

Expected evolution:

```text
Stage 0: lint + unit tests
Stage 1: ROS dependency/build/test
Stage 2: firmware compile/test
Stage 3: lightweight simulation/integration checks
```

Do not introduce heavyweight infrastructure unless the project actually needs it.

Do not add Jenkins, Kubernetes, deployment platforms, hardware farms, or complicated release automation merely because they are common in larger organizations.

CI must not pretend to replace supervised physical testing.

---

# 22. Experiment Rules

Experiments should answer an engineering question.

Where useful, record:

- objective,
- software commit/tag,
- hardware/configuration,
- procedure,
- input conditions,
- measurements,
- plots/results,
- conclusion.

Do not use vague statements such as "controller works well" when quantitative metrics are available or required.

Useful metrics may include:

- RMS tracking error,
- maximum error,
- rise time,
- settling time,
- overshoot,
- loop jitter,
- dropped packets,
- communication latency,
- repeatability,
- pose error,
- success rate.

Avoid committing huge raw logs directly into normal Git history.

---

# 23. Configuration and Calibration Rules

Prefer configuration files for robot-specific values rather than scattering constants through source code.

Examples include:

- joint limits,
- communication settings,
- controller gains,
- calibration transforms,
- camera parameters,
- robot-specific IDs.

Safety-critical configuration must be easy to review in Git diffs.

Do not overwrite calibration results without preserving enough context to reproduce or understand the change.

---

# 24. Documentation Rules

Update documentation in the same task when behavior, architecture, public interfaces, setup, or configuration changes materially.

Keep documentation factual.

Distinguish:

```text
planned
implemented
simulated
hardware-validated
measured
```

Do not state planned future capabilities as if they already exist.

Do not claim a feature is production-ready solely because it worked in a portfolio prototype.

---

# 25. Definition of Done

A feature should not be considered complete merely because it worked once.

For relevant changes, completion means:

- code/configuration is committed when the environment expects commits,
- behavior is repeatable,
- inputs/outputs are documented,
- failure cases are known,
- at least one meaningful test exists where practical,
- measurements exist when the feature makes performance claims,
- results are reproducible,
- the implementation can be explained clearly.

Apply this proportionally: a documentation typo does not need an experiment, and a controller change should not be treated like a documentation typo.

---

# 26. Secrets and Sensitive Files

Never commit:

- API keys,
- access tokens,
- passwords,
- private SSH keys,
- cloud credentials,
- Wi-Fi credentials,
- private certificates,
- `.env` files containing secrets.

If a secret is discovered in tracked or staged changes, stop and report it rather than propagating it.

Use examples/placeholders for configuration that requires credentials.

---

# 27. Generated Files

Do not commit ordinary generated build artifacts unless they are intentionally part of project evidence.

Typical generated paths should remain ignored:

```text
build/
install/
log/
__pycache__/
.pytest_cache/
IDE caches
compiled intermediates
```

Selected plots/reports may be committed when they represent deliberate experiment or portfolio evidence.

---

# 28. Code Quality

Prefer code that is:

- simple,
- explicit,
- testable,
- modular,
- readable,
- deterministic where real-time behavior requires it.

Avoid premature abstraction.

Avoid clever solutions that make hardware debugging harder.

Avoid adding advanced algorithms without a measurable reason.

Prefer an understood baseline first, then compare an advanced method when the experiment justifies it.

---

# 29. Compatibility and Dependency Changes

Do not upgrade major toolchains or dependency versions casually.

Changes to items such as:

- ROS distribution,
- compiler toolchain,
- MCU SDK,
- FreeRTOS version,
- vendor SDK,
- MoveIt major version,
- Python version,

can have system-wide effects.

Treat substantial dependency changes as explicit tasks, document the reason, and update reproducibility instructions.

---

# 30. No Speculative Hardware Rewrite Before Measurement

Before the physical robot is characterized, do not build large amounts of low-level control code around assumed capabilities.

First establish facts such as:

- what each joint can command,
- what each joint can measure,
- available feedback,
- actual protocol behavior,
- actual timing,
- actuator limits,
- fault behavior.

Use mocks/simulation for software architecture before hardware arrival, but keep the boundary between simulated assumptions and measured hardware facts explicit.

---

# 31. Working With Existing User Changes

The repository may contain user changes that are unrelated to the current task.

Always inspect before modifying.

Do not "clean up" unrelated work.

Do not revert a user's edits because they conflict with a preferred implementation. Adapt around them where possible or report the conflict.

When a task touches a file already modified by the user, preserve their changes unless the requested task clearly requires altering them.

---

# 32. Final Diff Review

Before finishing a coding task, inspect:

```bash
git diff --check
```

and the relevant diff/status where available.

Check for:

- accidental generated files,
- accidental vendor modifications,
- debugging prints,
- commented-out temporary code,
- credentials,
- unrelated formatting,
- unexpected large files,
- stale docs,
- missing tests.

Do not report completion before checking the final state.

---

# 33. Expected Final Task Report

At the end of a task, provide a concise report containing:

1. **What changed** — the implemented result.
2. **Validation** — exact tests/builds/checks run and their outcomes.
3. **Hardware status** — explicitly state whether physical hardware was used when relevant.
4. **Git status** — branch/commit state when relevant.
5. **Limitations or follow-ups** — only meaningful unresolved items.

Never claim a test passed if it was not run.

Never claim hardware validation if only mock/simulation validation occurred.

---

# 34. Agent Behavior for Ambiguous Technical Choices

When multiple reasonable implementations exist:

1. inspect existing repository patterns first,
2. prefer consistency with current architecture,
3. choose the smallest reversible solution,
4. avoid introducing a new dependency unless it provides clear value,
5. document a significant choice when needed.

Do not turn a small implementation task into an architecture redesign without strong justification.

---

# 35. Nested Agent Instructions

As the monorepo grows, more specific instructions may be added, for example:

```text
AGENTS.md
firmware/AGENTS.md
ros2_ws/AGENTS.md
experiments/AGENTS.md
```

Possible purpose:

- `firmware/AGENTS.md`: MCU language, RTOS, formatting, build/flash/test rules.
- `ros2_ws/AGENTS.md`: ROS package conventions and colcon tests.
- `experiments/AGENTS.md`: experiment metadata and data-handling rules.

Do not create nested files prematurely. Add them when the subsystem actually needs distinct rules.

---

# 36. Summary of Non-Negotiable Git Rules

For normal development:

```text
Do not develop directly on main.
Do not develop directly on dev when task branches are available.
Use short-lived task branches.
Target normal PRs to dev.
Use squash merge for task PRs.
Release dev to main only at milestones.
Do not autonomously merge protected branches.
Do not force-push.
Do not rewrite unrelated history.
Do not destroy unrelated user changes.
Run relevant tests/checks before completion.
Inspect final diff and status.
Keep Git history and PRs understandable.
```

For milestone releases:

```text
dev
  ↓
validate milestone
  ↓
PR to main
  ↓
CI / review
  ↓
merge
  ↓
tag / release
```

For this robotics project, Git discipline is part of the engineering evidence. The repository should make it possible to understand not just **what** was built, but **how it was developed, validated, and evolved**.
