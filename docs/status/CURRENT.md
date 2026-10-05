# Current Project State

**Updated:** 2026-10-05

**Milestone:** M1 — Stationary Arm Bring-Up

**Active focus:** establish the minimal reproducible firmware build and host-test foundation

**Working branch:** `feat/firmware-bootstrap`

This is the single living status document for `robotic-stack`. Update it when the milestone, verified state, blockers, or immediate next work changes.

## Current Objective

Establish a minimal, reproducible firmware foundation for the Waveshare RoArm-M3-S onboard ESP32 using ESP-IDF + FreeRTOS, while keeping reusable logic independent of the platform and avoiding unverified hardware assumptions.

## Confirmed Project Decisions

- The initial robot is the **Waveshare RoArm-M3-S**.
- The initial controller target is the robot's onboard ESP32.
- The firmware baseline is **ESP-IDF + FreeRTOS**.
- Portable logic must remain separate from ESP-IDF, FreeRTOS, and robot-specific composition code.
- Host-side testing is required for portable protocol, state-machine, safety, and math logic.

These are project decisions. They are not physical validation of the robot, servo bus, timing, limits, or fault behavior.

## Repository State

- The repository, Git workflow, PR template, and bootstrap CI exist.
- The long-term roadmap includes the custom firmware direction.
- The initial `firmware/` ownership boundaries and directory skeleton exist on the active feature branch.
- Project instructions, current-status ownership, canonical RoArm-M3-S naming, related documentation references, CI sanity checks, and the robot-specific platform directory name are aligned and published on the active feature branch.
- No firmware source code or embedded build configuration exists yet.
- No host-side or target-side firmware test harness exists yet.
- No physical robot behavior has been recorded as validated in the repository.

## Immediate Next Task

Add the smallest reproducible firmware build and host-test foundation without commanding physical motion.

### Objective

Create a minimal ESP-IDF application that builds for the selected target and a host-side unit-test harness that runs one trivial deterministic test, then document and automate the supported non-hardware checks.

### Reason

The project needs a verified build-and-test baseline before protocol, transport, state-machine, safety, or control logic is introduced. Establishing both paths now keeps portable logic testable and prevents later modules from depending on an undocumented local setup.

### In Scope

- select and document the initial ESP-IDF and compiler/toolchain versions needed for reproducible builds,
- resolve and document the portable host-language and test-framework choice, with C11 and Ceedling currently recommended,
- add the minimal ESP-IDF project and application entry point under the established ESP32 and RoArm-M3-S platform boundaries,
- ensure the application performs no actuator, servo-bus, or physical-motion operation,
- add the minimal host unit-test harness for portable firmware logic,
- add one trivial deterministic test that proves the host path compiles and executes,
- document exact setup, target-build, and host-test commands,
- add supported non-hardware build and test checks to CI,
- update this document with the verified result and any remaining environment limitations.

### Out of Scope

- implementing protocols, transports, OS abstractions, state machines, control logic, or physical motion,
- communicating with or configuring the smart-servo bus,
- adding robot-specific joint IDs, limits, directions, gains, rates, or other unverified hardware values,
- flashing the onboard ESP32 or running target-side tests on physical hardware,
- adding ROS 2 integration, simulation, deployment, or release automation,
- upgrading unrelated dependencies or reorganizing existing ownership boundaries.

### Required Validation

1. run the documented ESP-IDF target build from a clean generated-output state,
2. run the documented host test command and confirm the trivial test passes,
3. run every new non-hardware CI command locally when the environment supports it,
4. run the existing repository sanity checks and `git diff --check`,
5. inspect the final and staged diffs for generated files, secrets, debugging remnants, unrelated changes, and accidental hardware assumptions,
6. report target-build and host-test outcomes separately and identify any check that could not run because a required toolchain is unavailable.

### Acceptance Criteria

- A clean checkout can run the documented setup and ESP-IDF build procedure for the minimal application.
- A clean checkout can run the documented host test command and pass one deterministic test.
- Portable test code does not include ESP-IDF or FreeRTOS headers.
- The target application does not initialize or command actuators or the servo bus.
- Supported non-hardware checks run in CI.
- Toolchain and test-framework choices are recorded with exact versions or an explicit, reproducible version-selection mechanism.
- No generated build output, credentials, or unverified robot-specific values are committed.

### Hardware and Safety

No hardware is required. Build and test on the host only. Do not flash the ESP32, communicate with the servo bus, or command physical motion.

## Following Task

After the firmware foundation is validated, verify and document the exact smart-servo protocol and electrical interface from authoritative vendor material or supervised measurement. Then scope packet serialization and parsing as a portable, host-tested task without initiating physical motion.

## Open Questions

- Is the physical Waveshare RoArm-M3-S currently available for supervised testing?
- Which exact smart-servo protocol variant and electrical interface does the robot use?
- What recovery and flashing procedure is safe for the onboard ESP32?
- Which ESP-IDF version and compiler/toolchain versions will be pinned?
- Which host-side test framework best fits the first portable modules?
- What communication rate, feedback, limits, and fault behavior can be verified from documentation or measurement?
- Which ROS 2 distribution and Linux environment will be selected for the later high-level stack?

## Hardware Validation Status

- Vendor documentation review: not recorded
- Firmware build: not started
- Host tests: not started
- Target tests: not started
- Bench communication: not started
- Physical motion: not started

Do not interpret “not started” as evidence that a capability is unavailable. It means no result has been recorded in this repository.

## Completion Signal for This Stage

The firmware-foundation stage is complete when a clean checkout can build the minimal ESP-IDF target and run the documented host test suite reproducibly, with CI covering the non-hardware checks and no physical-hardware claims implied.

## Restart Here

1. Read this file.
2. Read `firmware/README.md`.
3. Inspect the active branch and recent commits.
4. Read the relevant section of `docs/plans/robotic-stack-plan.md`.
5. Define one small task with explicit acceptance criteria and validation.
