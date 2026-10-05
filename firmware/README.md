# Firmware

This directory contains MCU and RTOS-facing software for the robotics portfolio.

The initial firmware baseline is designed for:

- `ESP-IDF + FreeRTOS` on the Waveshare RoArm-M3-S onboard ESP32,
- a reusable firmware core that does not depend directly on ESP-IDF headers,
- host-side unit tests for portable logic,
- future migration to additional MCU platforms without rewriting the control and safety layers.

## Design Rules

- Keep `core/`, `control/`, and `estimation/` free of direct `freertos/` and `esp_*` includes.
- Keep the OS abstraction in `osal/` intentionally small.
- Keep hardware and vendor integration isolated under `platforms/`.
- Treat the RoArm firmware as the first backend of a reusable robotics firmware stack, not as a one-off project.
- Prefer testable logic functions over RTOS-heavy task implementations.

## Initial Structure

```text
firmware/
├── osal/
├── core/
├── drivers/
├── control/
├── estimation/
├── platforms/
└── tests/
```

## Ownership Boundaries

- `osal/`: narrow operating-system abstraction used by reusable modules
- `core/`: scheduler-adjacent coordination, safety, communication, diagnostics, timebase
- `drivers/`: device and transport interfaces plus reusable driver logic
- `control/`: controller, trajectory, saturation, and limit logic
- `estimation/`: filters and derived-state estimation
- `platforms/`: ESP-IDF integration and robot/platform-specific composition
- `tests/`: host, target, and fake components for verification

## Testing Direction

- `tests/host/` is for fast host-side unit tests of protocol, parsing, state-machine, and math logic.
- `tests/target/` is for ESP-IDF target-side tests.
- `tests/fakes/` is for reusable mocks, stubs, and deterministic fake devices.

The presence of a directory does not imply an implementation exists yet. It defines where that work belongs when it starts.
