# Git Workflow and Development Process

**Project:** Robotics Portfolio — Stationary Robot Arm → Mobile Manipulator  
**Repository model:** Unified monorepo  
**Development model:** Pull-request-based development with `main`, `dev`, and short-lived task branches  
**Recommended repository path for this document:** `docs/development/GIT_WORKFLOW.md`

---

## 1. Purpose

This document defines how development is organized for the robotics portfolio repository.

The project spans several software and hardware layers:

- embedded / MCU firmware,
- robot communication,
- low-level control,
- ROS 2,
- robot descriptions and configuration,
- simulation,
- perception and planning,
- calibration,
- experiments and measurement,
- tooling,
- documentation,
- later, a second mobile-manipulator platform.

Because these pieces evolve together, the project uses **one monorepo** with a common Git workflow, common CI, common documentation, and milestone-based releases.

The workflow is designed for a solo project, so it should provide professional engineering discipline without introducing unnecessary process overhead.

The central rule is:

> Every non-trivial change is developed on a short-lived branch, validated, reviewed through a pull request, and integrated into `dev`. `main` receives only milestone-quality releases.

---

# 2. Engineering Principles Behind the Workflow

The Git process exists to support the engineering goals of the project, not to become a project of its own.

The following principles take priority.

## 2.1 Keep `main` demonstrable

`main` represents a stable portfolio state.

A checkout of `main` should correspond to a meaningful, documented milestone that can be explained, built, and demonstrated.

Do not use `main` as the everyday development branch.

## 2.2 Keep `dev` integrated

`dev` is the integration branch for completed work that belongs to the next milestone.

`dev` may be ahead of the latest release, but it should still normally build and pass automated checks.

Do not intentionally merge broken or half-implemented work into `dev` simply to store it remotely.

## 2.3 Keep feature branches short-lived

Task branches should solve one coherent problem.

Good examples:

- add the serial transport abstraction,
- add RoArm joint-state parsing,
- add the robot URDF,
- add a watchdog state,
- add trajectory unit tests,
- add an experiment plotting tool.

Poor examples:

- `work`,
- `robot-stuff`,
- `month-1-everything`,
- a branch containing unrelated firmware, ROS, documentation, and calibration changes for several weeks.

## 2.4 Prefer small validated steps

A robotics project contains uncertainty. Hardware behavior, servo capabilities, timing limits, protocol details, backlash, feedback quality, and control authority may differ from assumptions.

Therefore:

1. make one change,
2. validate it,
3. collect evidence when relevant,
4. integrate it,
5. continue from the validated state.

Avoid large speculative implementations based on unverified hardware assumptions.

## 2.5 Separate upstream/vendor code from project-owned code

Waveshare or other third-party packages should be treated as dependencies unless there is a deliberate reason to fork them.

The repository should make it clear which engineering work is original project code and which software comes from external vendors or open-source projects.

## 2.6 A working demo is not automatically "done"

For this project, completion means more than making something move once.

Where applicable, a completed change should be:

- committed,
- repeatable,
- documented,
- tested,
- measured,
- reproducible,
- understandable later.

---

# 3. Repository Branch Model

The project uses the following branch hierarchy:

```text
                         feat/...
                        /
                       /       fix/...
                      /       /
                     v       v

dev  ----------------o---o---o------o--------------------
                      \             /
                       \           /
                        \         /
main --------------------o-------o------------------------
                         v0.1.0  v0.2.0
```

The permanent branches are:

```text
main
dev
```

All other branches are temporary.

---

# 4. Branch Responsibilities

## 4.1 `main`

`main` is the **stable release branch**.

It should contain only:

- completed milestone releases,
- urgent release fixes,
- release documentation.

Every significant merge into `main` should normally correspond to a version tag.

Examples:

```text
v0.1.0  Stationary arm bring-up
v0.2.0  Reusable embedded control framework
v0.3.0  ROS 2 hardware integration
v0.4.0  Vision and calibration
v0.5.0  Stationary-arm portfolio scenario
```

Later milestones can continue the sequence.

### Rules for `main`

- Never develop directly on `main`.
- Never use `main` as a scratch branch.
- Merge into `main` through a PR.
- Require CI to pass before merge.
- Tag milestone releases.
- Do not force-push.
- Do not rewrite release history.

---

## 4.2 `dev`

`dev` is the **integration branch for the next release**.

Completed feature and fix branches normally target `dev`.

`dev` should contain the newest integrated project state while remaining reasonably healthy.

### Rules for `dev`

- Do not develop directly on `dev` except for exceptional repository-administration changes where a PR is genuinely unnecessary.
- Prefer a branch and PR even when working alone.
- Every merged PR should be independently understandable.
- CI should pass before merge.
- Do not use `dev` to preserve unfinished experiments; keep them on their task branch.

---

# 5. Task Branch Types

Use a short-lived branch for each logical change.

Recommended prefixes:

| Prefix | Purpose | Example |
|---|---|---|
| `feat/` | New capability | `feat/roarm-serial-transport` |
| `fix/` | Bug fix | `fix/serial-timeout` |
| `test/` | Tests or test infrastructure | `test/protocol-parser` |
| `docs/` | Documentation | `docs/software-architecture` |
| `ci/` | CI/CD configuration | `ci/initial-github-actions` |
| `refactor/` | Structural change without intended behavior change | `refactor/transport-interface` |
| `chore/` | Repository maintenance | `chore/update-gitignore` |
| `experiment/` | Experimental work that may later become production code | `experiment/joint1-step-response` |
| `hotfix/` | Urgent correction to released `main` | `hotfix/unsafe-joint-limit` |

Use lowercase kebab-case.

Good:

```text
feat/mock-robot-transport
feat/roarm-joint-state-interface
fix/watchdog-reset-condition
ci/add-ros-build
experiment/joint1-pd-baseline
```

Avoid:

```text
feature1
newstuff
ercan-test
final-version
changes
```

---

# 6. Normal Development Lifecycle

The normal lifecycle is:

```text
Issue / task
    ↓
Update local dev
    ↓
Create task branch
    ↓
Implement
    ↓
Test locally
    ↓
Commit
    ↓
Push branch
    ↓
Open PR → dev
    ↓
CI
    ↓
Self-review / review
    ↓
Squash and Merge
    ↓
Delete task branch
```

Each stage is described below.

---

# 7. Step 1 — Define the Work Before Coding

For non-trivial work, create a GitHub Issue or at least define a clearly scoped task before implementation.

A good issue should answer:

- What capability is missing?
- Why is it needed now?
- What is inside scope?
- What is explicitly outside scope?
- How will success be verified?
- Is real hardware required?
- Which milestone does it support?

Example:

```markdown
# Add mock RoArm transport

## Goal
Allow ROS/interface development before physical hardware arrives.

## Scope
- Define transport interface.
- Add mock implementation.
- Support command send / telemetry receive behavior used by tests.
- Add unit tests.

## Out of scope
- Real serial communication.
- Servo tuning.
- ROS 2 hardware interface.

## Acceptance criteria
- Mock accepts supported command objects.
- Deterministic telemetry can be injected.
- Unit tests run in CI.
```

This prevents a small task from quietly turning into an uncontrolled subsystem rewrite.

---

# 8. Step 2 — Start From an Updated `dev`

Before starting normal feature work:

```bash
git switch dev
git pull --ff-only origin dev
```

Then create the task branch:

```bash
git switch -c feat/mock-robot-transport
```

The task branch should normally start from `dev`, not `main`.

Exception: a true production/release hotfix starts from `main`.

---

# 9. Step 3 — Implement in Small Logical Changes

Do not wait until the entire feature is complete before making any commits.

Commits should represent understandable development steps.

Example:

```text
feat: define robot transport interface
test: add mock transport unit tests
feat: implement deterministic mock telemetry
docs: document transport abstraction
```

Temporary local commits are acceptable during development, but the final PR history should be understandable.

Because feature PRs are normally squash-merged into `dev`, the internal feature-branch history does not need to be perfect. It should still avoid meaningless noise where practical.

---

# 10. Commit Message Convention

Use a Conventional-Commit-style format:

```text
<type>: <short imperative summary>
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
feat: add mock RoArm transport
fix: reject malformed joint-state packets
test: cover command serialization edge cases
docs: document robot communication layers
ci: run Python checks on pull requests
refactor: separate protocol encoding from transport
```

For a scoped subsystem, optionally use:

```text
feat(firmware): add watchdog state
feat(ros2): publish stationary arm joint states
fix(protocol): handle partial serial frame
```

### Commit-message rules

- Use imperative wording.
- Keep the first line concise.
- Explain **why** in the body when the reason is not obvious.
- Do not use messages such as `fix`, `changes`, `final`, `update`, or `stuff` without context.
- Do not include secrets, credentials, personal tokens, or local machine paths.

---

# 11. Step 4 — Validate Before Opening the PR

Run all relevant checks that can reasonably run on the current machine.

Validation depends on the affected layer.

## 11.1 Documentation-only change

Check:

- Markdown renders correctly.
- Links and paths are valid where practical.
- Commands shown in docs are still correct.

## 11.2 Python/tooling change

Typical checks may include:

```text
format/lint
type checks if configured
unit tests
```

## 11.3 C/C++/firmware change

Typical checks may include:

```text
format/lint
host-side unit tests
firmware build
static checks if configured
```

## 11.4 ROS 2 change

Typical checks may include:

```text
rosdep resolution
colcon build
colcon test
launch/config validation
simulation test when applicable
```

## 11.5 Hardware-dependent change

Separate automated software validation from physical testing.

Record explicitly:

```text
Automated tests: PASS
Simulation: PASS
Hardware test: NOT RUN — hardware not available
```

Never claim hardware validation when only simulation or mocks were used.

---

# 12. Step 5 — Push and Open a Pull Request

Push the branch:

```bash
git push -u origin feat/mock-robot-transport
```

Open a PR targeting:

```text
feat/... → dev
fix/...  → dev
docs/... → dev
ci/...   → dev
```

Normal feature PRs should **not** target `main`.

---

# 13. Pull Request Format

Every PR should make the change easy to understand several months later.

Recommended template:

```markdown
## What

What changed?

## Why

Why is this needed?

## Changes

- ...
- ...

## Validation

- [ ] Unit tests
- [ ] Build
- [ ] Simulation
- [ ] Hardware test
- [ ] Manual verification

Mark non-applicable checks and explain hardware tests that were not run.

## Evidence

Logs, screenshots, plots, measurements, or experiment references when relevant.

## Risks / Limitations

Known limitations, assumptions, or follow-up work.

## Related

Closes #...
```

For robotics/control work, the **Evidence** section is especially important.

Useful evidence includes:

- tracking plots,
- RMS error,
- timing/jitter measurements,
- serial logs,
- packet-loss measurements,
- RViz screenshots,
- experiment folder links,
- fault-recovery observations.

---

# 14. PR Size and Scope

Prefer PRs that answer one question.

Good:

```text
PR: Add serial framing and parser
PR: Add joint-state message model
PR: Add mock transport
PR: Add ROS 2 joint-state publisher
```

Less desirable:

```text
PR: Add all communication, ROS, control, tests, docs, simulation,
and refactor directory structure
```

A large PR is acceptable when the change is inherently atomic, but splitting should be the default.

A useful rule:

> If the PR description needs several unrelated "Why" explanations, it probably contains several PRs.

---

# 15. Self-Review Before Merge

Even in a solo project, review the PR as if another engineer wrote it.

Check:

- Is the change smaller than it needs to be?
- Is any unrelated cleanup mixed into the PR?
- Are names clear?
- Are failure cases handled?
- Are assumptions documented?
- Are tests meaningful rather than only testing implementation details?
- Does code cross an architecture boundary unnecessarily?
- Is vendor code being modified when a wrapper would be better?
- Were hardware-dependent statements actually measured?
- Does the PR introduce configuration that belongs outside source code?
- Did any credentials, generated files, build artifacts, or large raw datasets enter Git accidentally?

Then review the actual diff, not only the final program behavior.

---

# 16. Merge Strategy

## 16.1 Task branch → `dev`

Default strategy:

> **Squash and Merge**

Why:

- `dev` remains readable.
- Experimental/fixup commits do not pollute integration history.
- Each PR becomes one logical commit.
- Reverting a PR is easier.

The squash commit should use a clear conventional-style message.

Example:

```text
feat(protocol): add RoArm serial transport (#18)
```

After merge, delete the remote task branch.

---

## 16.2 `dev` → `main`

Use a release PR.

Do **not** squash months of integrated development into one giant anonymous commit if preserving the milestone history is useful.

A normal merge commit is preferred for milestone releases because it preserves the set of PRs that formed the milestone.

Example:

```text
release: milestone 1 stationary arm bring-up
```

After the merge:

1. verify `main`,
2. create the version tag,
3. create the GitHub Release if desired,
4. record milestone results.

---

# 17. Milestone Release Workflow

A milestone release occurs only after the milestone exit criteria are satisfied.

Workflow:

```text
Completed PRs
    ↓
dev
    ↓
Milestone validation
    ↓
Release PR: dev → main
    ↓
CI
    ↓
Final review
    ↓
Merge
    ↓
Tag
    ↓
Release notes
```

Recommended tags:

```text
v0.1.0
v0.2.0
v0.3.0
...
```

A `v1.0.0` release can be reserved for the final portfolio state if that feels meaningful.

---

# 18. Versioning Policy

Use pragmatic semantic versioning.

For this portfolio:

### Major

```text
1.x.x
```

Use for a major externally meaningful architecture or portfolio release.

### Minor

```text
0.1.0
0.2.0
0.3.0
```

Use for project milestones and substantial new integrated capability.

### Patch

```text
0.3.1
0.3.2
```

Use for fixes to a released milestone that do not represent a new milestone.

---

# 19. Recommended Milestone Mapping

A sensible initial mapping is:

| Tag | Milestone |
|---|---|
| `v0.0.1` | Repository / CI bootstrap, optional pre-hardware checkpoint |
| `v0.1.0` | Stationary robot command and measurement bring-up |
| `v0.2.0` | Reusable embedded control framework |
| `v0.3.0` | ROS 2 controls the physical stationary robot |
| `v0.4.0` | Vision-based object pose integrated |
| `v0.5.0` | Standalone stationary-arm case study complete |
| `v0.6.0` | Mobile platform bring-up |
| `v0.7.0` | Localization/navigation milestone |
| `v0.8.0` | Two-robot collaborative scenario |
| `v0.9.0` | Precision scenario and final technical validation |
| `v1.0.0` | Final cleaned portfolio release |

The exact version numbers may change; the important rule is that tags correspond to meaningful reproducible states.

---

# 20. Release PR Requirements

Before `dev` is merged into `main`, verify:

## Code / build

- [ ] CI passes.
- [ ] Required software builds from a clean checkout.
- [ ] Dependencies are pinned or documented appropriately.
- [ ] No known release-blocking regression exists.

## Hardware / robotics

- [ ] Milestone hardware capability is reproducible.
- [ ] Required safety checks exist.
- [ ] Relevant limits/configuration are documented.
- [ ] Hardware tests are distinguished from simulation tests.

## Evidence

- [ ] Required measurements exist.
- [ ] Experiment inputs/configuration are recorded.
- [ ] Relevant plots/logs are saved.
- [ ] Known limitations are written down.

## Documentation

- [ ] README reflects the current project state.
- [ ] Architecture docs are current enough to be useful.
- [ ] Significant decisions have ADR/decision records.
- [ ] Setup/build instructions are usable.
- [ ] Release notes summarize the milestone.

---

# 21. Hotfix Workflow

A hotfix is reserved for an important problem in the currently released `main` state.

Examples:

- unsafe joint limit,
- broken release setup instructions,
- critical command validation bug,
- release cannot build.

Create from `main`:

```bash
git switch main
git pull --ff-only origin main
git switch -c hotfix/unsafe-joint-limit
```

Then:

```text
hotfix/... → PR → main
```

After the hotfix is released, ensure the same fix is present in `dev`.

Usually:

```text
main/hotfix result → merge or cherry-pick into dev
```

Do not allow `main` and `dev` to diverge permanently after a hotfix.

---

# 22. CI Strategy

CI should grow with the project rather than being designed all at once.

The purpose of CI is:

> Re-run important software checks automatically on a clean environment whenever integration is proposed.

CI is not a substitute for physical robot testing.

---

# 23. CI Stage 0 — Repository Bootstrap

Initial checks can be minimal:

```text
Markdown/repository sanity
Python lint
Python unit tests
```

Trigger CI on:

```text
pull_request → dev
pull_request → main
push → dev
push → main
```

The most important initial outcome is simply:

```text
PR opened
   ↓
checks run automatically
   ↓
PASS / FAIL is visible
```

---

# 24. CI Stage 1 — ROS 2

When ROS 2 packages become part of the repository, add:

```text
install dependencies
rosdep
colcon build
colcon test
```

If external/vendor ROS repositories are imported using a `.repos` file, CI should use the same dependency definition as local development.

Do not allow the CI environment and developer environment to silently use different dependency versions.

---

# 25. CI Stage 2 — Firmware

When custom MCU firmware exists, add what can run without hardware:

```text
firmware compile
format/lint
host-side unit tests where practical
static analysis if useful
```

A firmware build in CI proves that code compiles. It does **not** prove that the robot is safe or that the control loop behaves correctly.

---

# 26. CI Stage 3 — Simulation / Integration

Later, add lightweight integration tests where they provide value:

- message serialization round trips,
- mock transport integration,
- ROS node startup checks,
- URDF/Xacro validation,
- controller configuration validation,
- deterministic simulation tests.

Keep CI reasonably fast. Long experimental runs belong in an experiment workflow, not in every PR.

---

# 27. Hardware-in-the-Loop Policy

Do not make physical hardware testing a required cloud CI step during the early project.

Reasons:

- the robot is not continuously available to a GitHub runner,
- movement can require supervision,
- a software bug can cause physical motion,
- hardware tests may be nondeterministic,
- safety requires deliberate setup.

Instead, PRs should clearly record hardware validation status.

Example:

```text
CI: PASS
Simulation: PASS
Bench test: PASS
Physical motion test: PASS — low-speed joint 1 only
```

Later, a dedicated local HIL workflow may be added if it becomes genuinely useful.

---

# 28. GitHub Branch Protection

Recommended settings for `main`:

- require a pull request before merging,
- require CI status checks,
- block force pushes,
- block deletion,
- optionally require branch to be up to date before merge.

Recommended settings for `dev`:

- require a pull request before merging,
- require CI status checks,
- block force pushes.

Because this is a solo project, requiring approval from another person is unnecessary unless collaborators join later.

The PR itself still provides a valuable review record.

---

# 29. GitHub Merge Settings

Recommended:

```text
Allow squash merging: YES
Allow merge commits: YES
Allow rebase merging: optional
Automatically delete head branches: YES
```

Use:

- squash merge for task branches → `dev`,
- merge commit for milestone `dev` → `main` release PRs.

---

# 30. Issue Management

Issues should represent actionable engineering work, defects, research questions, or explicit technical debt.

Suggested labels:

```text
firmware
ros2
control
estimation
perception
simulation
hardware
tooling
documentation
experiment
ci
bug
safety
blocked
research
```

Priority labels can stay simple:

```text
must
should
stretch
```

This directly mirrors the project scope-management philosophy.

---

# 31. Issue → Branch → PR Traceability

Where possible:

```text
Issue #14
   ↓
feat/14-mock-transport
   ↓
PR #19
   ↓
Squash merge into dev
```

The branch number is optional, but useful.

The PR can include:

```text
Closes #14
```

This creates a lightweight engineering record without requiring additional project-management software.

---

# 32. Architecture Decision Records

Use ADRs for decisions likely to matter later.

Recommended location:

```text
docs/decisions/
```

Examples:

```text
ADR-0001-use-monorepo.md
ADR-0002-use-dev-integration-branch.md
ADR-0003-keep-vendor-code-external.md
ADR-0004-robot-transport-abstraction.md
ADR-0005-ros-distribution.md
```

An ADR should normally contain:

```markdown
# ADR-XXXX: Decision title

## Status
Accepted / Superseded / Proposed

## Context
What problem or constraint required a decision?

## Decision
What was chosen?

## Alternatives
What else was considered?

## Consequences
What improves and what becomes harder?

## Revisit when
What future evidence should trigger reconsideration?
```

Do not create ADRs for trivial choices.

---

# 33. Dependency and Vendor-Code Policy

Third-party code should not silently become project-owned code.

Prefer:

```text
dependencies/
    roarm.repos
```

or a documented dependency mechanism rather than copying external repositories into project directories.

If a third-party project must be modified:

1. document why a wrapper/configuration is insufficient,
2. consider a fork,
3. pin the fork/revision,
4. keep local project code separate,
5. document deviations from upstream.

Never modify copied vendor code without making ownership/provenance clear.

---

# 34. Generated Files and Build Artifacts

Do not commit ordinary generated output unless the artifact is intentionally part of the portfolio evidence.

Normally ignore:

```text
build/
install/
log/
__pycache__/
.pytest_cache/
.vscode/ user-specific files
IDE caches
compiled firmware artifacts
large temporary logs
```

Some generated outputs may legitimately belong in the repo when they are final engineering evidence, such as selected result plots or a small representative dataset.

The distinction should be deliberate.

---

# 35. Experiment Data Policy

Experiments are first-class engineering work.

Recommended structure:

```text
experiments/
└── stationary_arm/
    └── 2026-09-12-joint1-step-response/
        ├── README.md
        ├── config.yaml
        ├── processed.csv
        └── plots/
```

For each meaningful experiment record:

- objective,
- hardware/software version,
- commit or tag,
- configuration,
- procedure,
- measured result,
- conclusion.

Avoid committing very large raw datasets directly to normal Git history.

Use a deliberate data-storage strategy if raw logs become large.

---

# 36. Hardware Configuration Policy

Separate configuration from code where practical.

Examples:

```text
robots/stationary_arm/config/joint_limits.yaml
robots/stationary_arm/config/communication.yaml
robots/stationary_arm/calibration/...
```

Do not scatter measured joint limits, serial settings, controller gains, camera transforms, or calibration constants across unrelated source files.

Changes to safety-relevant configuration should be easy to review in a diff.

---

# 37. Hardware Safety and Git Reviews

Changes that can affect physical motion deserve extra review attention.

Examples:

- joint limits,
- motor direction,
- maximum velocity,
- maximum acceleration,
- watchdog timing,
- enable/disable behavior,
- fault reset behavior,
- trajectory execution,
- emergency-stop handling.

PR descriptions for such changes should explain:

```text
What can physically change?
What limits apply?
How was it tested safely?
What failure mode exists?
```

Start physical validation with the smallest safe scope practical.

---

# 38. Firmware Flashing and Factory Firmware

Custom firmware development should be treated separately from ordinary application code changes.

Before replacing vendor firmware:

- save/document the factory firmware recovery method,
- record board/firmware versions,
- verify flashing and recovery procedure,
- ensure the project can return to a known working state,
- avoid combining first-time flashing with unrelated control changes.

A firmware-flashing change should have an explicit procedure and rollback path.

---

# 39. Documentation as Part of Development

Documentation should change in the same PR as the implementation when the implementation changes a public interface or architectural assumption.

Examples:

- a communication protocol change updates protocol docs,
- a new configuration option updates setup/configuration docs,
- an architecture change updates diagrams or ADRs,
- a new experiment adds its experiment record.

Avoid a large undocumented implementation followed by a promise to document everything months later.

---

# 40. Definition of Done for a PR

Not every checkbox applies to every PR, but the relevant ones should be satisfied.

## General

- [ ] The requested behavior is implemented.
- [ ] The scope is coherent.
- [ ] No unrelated changes are included.
- [ ] Code/configuration naming is understandable.
- [ ] Known limitations are documented.

## Validation

- [ ] Relevant unit tests pass.
- [ ] Relevant build passes.
- [ ] Relevant simulation test passes.
- [ ] Hardware testing is performed when required and available.
- [ ] Hardware testing that was not performed is explicitly stated.

## Engineering evidence

- [ ] Relevant measurements are recorded.
- [ ] Results are reproducible.
- [ ] Failure cases are understood.

## Documentation

- [ ] Public interfaces are documented.
- [ ] Architecture docs are updated if necessary.
- [ ] Significant decisions have an ADR/decision entry if necessary.

## Git

- [ ] PR targets the correct branch.
- [ ] CI passes.
- [ ] No secrets/build junk/large accidental files are included.
- [ ] PR title is clear.

---

# 41. Special Case — Experimental Branches

Some robotics work begins as research rather than a committed design.

Use:

```text
experiment/<topic>
```

when the purpose is explicitly to answer a technical question.

Example:

```text
experiment/joint1-control-frequency
```

Possible outcome A:

```text
experiment produces useful result
        ↓
write experiment report / ADR
        ↓
production implementation created as feat/... PR
```

Possible outcome B:

```text
experiment shows approach is unsuitable
        ↓
record result
        ↓
close branch without production merge
```

Not every experiment must become product code.

---

# 42. Avoid Long-Lived Personal Branches

Do not create a permanent branch such as:

```text
ercan-dev
my-work
experimental-main
```

The repository already has `dev` for integrated development.

Task branches should be temporary and disposable after integration.

---

# 43. Avoid Direct Development on `dev`

It is tempting in a solo project to write everything directly on `dev`.

Avoid that habit because it removes:

- PR-level CI gates,
- an explicit change boundary,
- a place to self-review the diff,
- issue/PR traceability,
- simple rollback at the PR level.

Even a one-person portfolio benefits from lightweight PR discipline.

---

# 44. Avoid Using Git as Backup Only

Git is not only a place to store code.

The repository should also communicate:

- how the system evolved,
- what decisions were made,
- what was measured,
- which capabilities belonged to each milestone,
- what third-party software was used,
- what was actually validated.

This is particularly important because the repository is part of the final engineering portfolio.

---

# 45. Typical Daily Workflow

Example:

```bash
# 1. Start from latest integrated state
git switch dev
git pull --ff-only origin dev

# 2. Create task branch
git switch -c feat/roarm-protocol-parser

# 3. Work and inspect changes
git status
git diff

# 4. Stage deliberately
git add <files>

# 5. Commit
git commit -m "feat(protocol): add response parser"

# 6. Run relevant tests
# project-specific commands here

# 7. Push
git push -u origin feat/roarm-protocol-parser

# 8. Open PR targeting dev
```

After merge:

```bash
git switch dev
git pull --ff-only origin dev
git branch -d feat/roarm-protocol-parser
```

---

# 46. Example: Pre-Hardware Feature

Task:

```text
Create a mock transport for RoArm development.
```

Flow:

```text
Issue #7
   ↓
feat/7-mock-roarm-transport
   ↓
implement interface + mock + tests
   ↓
local tests
   ↓
PR #11 → dev
   ↓
CI
   ↓
Squash merge
```

Squash commit:

```text
feat(protocol): add mock RoArm transport (#11)
```

---

# 47. Example: Hardware Bring-Up Feature

Task:

```text
Establish bidirectional communication with the physical Waveshare RoArm-M3-S.
```

Suggested branch:

```text
feat/roarm-bidirectional-serial
```

PR evidence might include:

```text
- Factory firmware version recorded.
- Serial connection established.
- 1,000 request/response cycles tested.
- Invalid response counter recorded.
- Communication-loss behavior documented.
- Only low-speed supervised motion used.
```

That is significantly stronger portfolio evidence than simply writing "serial works".

---

# 48. Example: Control Experiment

Task:

```text
Determine a baseline controller for joint 1.
```

Possible branch:

```text
experiment/joint1-pd-baseline
```

Experiment record:

```text
experiments/stationary_arm/2026-09-XX-joint1-pd-baseline/
```

After evaluating results:

```text
ADR: choose PD baseline
        ↓
feat/joint-pd-controller
        ↓
production-quality controller PR → dev
```

This keeps exploratory analysis separate from the reusable implementation.

---

# 49. Recommended Pre-Hardware PR Sequence

Before the robot arrives, a clean sequence could be:

```text
PR 1  chore: bootstrap monorepo structure
PR 2  docs: define git and development workflow
PR 3  ci: add initial repository checks
PR 4  build: define reproducible ROS dependency setup
PR 5  feat: add stationary-arm simulation/model integration
PR 6  feat: define robot transport abstraction
PR 7  feat: add mock robot transport
PR 8  test: add protocol and transport unit tests
```

Do not force this exact numbering. The principle is to keep infrastructure changes separated and reviewable.

---

# 50. Recommended GitHub Repository Settings Checklist

After repository creation:

- [ ] Create `dev` from `main`.
- [ ] Set `main` as default branch if you want visitors to land on stable releases.
- [ ] Protect `main`.
- [ ] Protect `dev`.
- [ ] Require PRs for both branches.
- [ ] Require CI checks once CI exists.
- [ ] Allow squash merging.
- [ ] Allow merge commits.
- [ ] Automatically delete merged task branches.
- [ ] Add PR template.
- [ ] Add issue labels.
- [ ] Add `.gitignore` appropriate to ROS 2, C/C++, Python, editors, and embedded tooling.
- [ ] Add `AGENTS.md` at repository root for Codex instructions.

---

# 51. Minimal Review Checklist for Every PR

Before pressing Merge, answer:

```text
1. Does this PR do one coherent thing?
2. Does CI pass?
3. Did I inspect the diff myself?
4. Did I accidentally modify vendor/generated files?
5. Is the behavior tested at the correct level?
6. If hardware matters, what did I actually test on hardware?
7. Is any important measurement or decision missing?
8. Can I understand this PR six months from now?
```

If the answer to all relevant questions is yes, merge it.

---

# 52. Final Workflow Summary

Normal work:

```text
Issue
  ↓
branch from dev
  ↓
implement
  ↓
local validation
  ↓
commit + push
  ↓
PR to dev
  ↓
CI + self-review
  ↓
squash merge
  ↓
delete branch
```

Milestone release:

```text
dev
  ↓
full milestone validation
  ↓
release PR to main
  ↓
CI + documentation review
  ↓
merge commit
  ↓
tag
  ↓
release notes
```

Emergency released-state fix:

```text
main
  ↓
hotfix branch
  ↓
PR to main
  ↓
patch release
  ↓
propagate fix back to dev
```

The process should remain lightweight enough that it helps development rather than slowing it down.

The repository is ultimately part of the portfolio. Its history should therefore demonstrate the same qualities as the robot itself:

> **reliable, measured, explainable, reproducible engineering.**
