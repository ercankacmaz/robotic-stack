# Drivers

`drivers/` contains project-owned hardware interface boundaries and reusable device logic.

Expected responsibilities include:

- actuator abstraction,
- smart-servo protocol handling,
- sensor interfaces,
- transport adapters,
- device capability mapping.

Direct vendor SDK and board-specific calls should stay in `platforms/` unless there is a strong reason not to.
