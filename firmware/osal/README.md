# OSAL

`osal/` contains the project-owned operating-system abstraction layer.

This layer should expose only the primitives that reusable firmware code actually needs, such as:

- mutexes,
- semaphores,
- queues or mailboxes,
- event signaling,
- monotonic time,
- delays,
- critical sections,
- ISR-safe notification variants where required.

Do not mirror the full RTOS API. Keep this layer small enough that a future platform port stays realistic.
