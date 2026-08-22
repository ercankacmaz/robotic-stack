# Tests

`tests/` contains embedded-firmware verification assets.

Initial split:

- `host/`: unit tests that run on the development machine
- `target/`: tests that execute in the embedded target environment
- `fakes/`: shared fake devices, fake transports, fake clocks, and mocks

Prefer placing logic under test outside `platforms/` so it can be exercised here without target hardware.
