# Core

`core/` contains reusable coordination and system-level firmware logic.

This area owns:

- scheduling-facing module orchestration,
- state machines,
- watchdog behavior,
- communication framing and dispatch,
- diagnostics,
- common timebase logic.

Keep this code independent from platform SDK details whenever possible.
