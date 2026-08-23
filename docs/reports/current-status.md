# Current Status

## Snapshot

- Date: August 23, 2026
- Milestone: M1
- Active focus: stationary arm firmware foundation
- Current branch: `feat/firmware-bootstrap`
- Hardware status: robot not yet available
- Firmware baseline: `ESP-IDF + FreeRTOS`
- Overall status: on track, with real hardware validation blocked until the robot arrives

## Current Goal

Establish a reusable firmware foundation for the RoArm M3 onboard ESP32 before hardware bring-up begins.

## Decisions Already Made

- The first firmware target is the RoArm M3 onboard ESP32.
- Firmware v1 uses `ESP-IDF + FreeRTOS`.
- Portability should come from project architecture rather than RTOS choice alone.
- Reusable logic must stay separate from ESP-IDF and platform-specific code.
- Host-side unit testing is required for portable embedded logic.

## Repository State

- `firmware/` skeleton exists.
- `osal/`, `core/`, `drivers/`, `control/`, `estimation/`, `platforms/`, and `tests/` are defined.
- ESP32-specific and RoArm-specific platform directories exist.
- No firmware source code exists yet.
- No embedded build system is added yet.
- No host-side or target-side unit test harness is added yet.

## Completed So Far

- Long-term robotics project plan is in place.
- Firmware v1 RTOS direction was selected.
- Initial firmware directory structure was created.
- Firmware, platform, and test ownership boundaries were documented.

## Immediate Next Steps

1. Define the first OSAL interface surface.
2. Define the transport interface for servo and host communication.
3. Add the first host-side unit test harness.
4. Implement smart-servo packet encode/decode logic with tests.
5. Define the first stationary-arm application wiring boundary.

## First Implementation Targets

- `firmware/osal/`
  - time
  - mutex
  - queue
  - event signaling
- `firmware/drivers/transport/`
  - generic transport interface
  - UART-oriented transport contract
- `firmware/drivers/smart_servo/`
  - packet format
  - checksum or CRC
  - frame parser
- `firmware/core/state_machine/`
  - enable
  - disable
  - fault
  - reset-fault
- `firmware/tests/host/`
  - unit test runner
  - fake clock
  - fake transport

## Open Questions

- Which exact smart-servo protocol variant is used by the RoArm M3 servos?
- What update rate is realistically achievable on the servo communication path?
- Should the onboard ESP32 run in single-core mode for simpler timing behavior?
- Which host-side unit test stack should be used first: Ceedling/Unity or CMake-based host tests?

## Blockers

- Robot hardware is not yet available.
- Exact servo communication details may still need confirmation from vendor documentation or experiments.
- Real timing and bandwidth limits cannot be validated yet.

## Deferred Until Hardware Arrival

- Real UART timing validation
- Servo bus stress testing
- Joint mapping validation
- Safety limit tuning
- End-to-end command and telemetry tests on the real robot

## Validation Status

- Unit tests: not started
- Host test harness: not started
- Firmware build: not started
- CI for firmware: bootstrap CI only
- Bench validation: not started
- Physical robot validation: not started

## Restart Here

If returning after a break:

1. Read `docs/plans/robotic-stack-plan.md`.
2. Read `firmware/README.md`.
3. Check the latest firmware branch or PR.
4. Start with OSAL and transport interfaces before platform-specific code.

## Important Files

- `docs/plans/robotic-stack-plan.md`
- `firmware/README.md`
- `firmware/osal/README.md`
- `firmware/platforms/esp32_idf/README.md`
- `firmware/platforms/stationary_arm_roarm_m3/README.md`
