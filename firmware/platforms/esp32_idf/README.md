# ESP32 IDF Platform

`platforms/esp32_idf/` is the vendor and RTOS integration area for the first firmware target.

This subtree should contain:

- ESP-IDF project glue,
- FreeRTOS-backed OSAL bindings,
- ESP32 peripheral adapters,
- board-support code needed by the current platform target.

Keep reusable logic out of this subtree unless it is genuinely ESP-IDF-specific.
