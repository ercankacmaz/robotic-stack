# Platforms

`platforms/` contains the code that is intentionally platform-specific.

This includes:

- MCU SDK integration,
- RTOS bindings,
- board support,
- startup and configuration glue,
- robot-specific application composition.

Reusable firmware code should depend on interfaces defined outside this directory, while this directory provides the concrete implementations.
