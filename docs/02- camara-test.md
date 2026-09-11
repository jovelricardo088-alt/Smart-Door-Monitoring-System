# Stage 2: Camera Test

**Goal:** Confirm the ESP32-CAM's onboard OV2640 camera and WiFi streaming work correctly using the standard CameraWebServer example, before adding any door-monitoring logic.

## Hardware
- AI Thinker ESP32-CAM
- CP2102 USB to TTL (UART) converter
- Jumper wires

## Wiring
| CP2102 | ESP32-CAM |
|---|---|
| TX | RX (U0R) |
| RX | TX (U0T) |
| GND | GND |
| 5V | 5V |

GPIO0 → GND, connected only while flashing new code.

## Problem: "No Serial Data Received" (again — but much worse this time)

```
A fatal error occurred: Failed to connect to ESP32: No serial data received.
```

This was the same error from Stage 1, but it took far longer to solve here. Two separate issues were stacked on top of each other:

**Cause 1: Insufficient power.** The camera + WiFi draw much more current than a bare ESP32 running a blink sketch. The CP2102's onboard 5V/3.3V regulator couldn't supply enough current, causing the board to brown out or fail to stay powered during flashing. Fix: powered the ESP32-CAM's 5V pin from an external 5V supply instead of relying on the CP2102 alone.

**Cause 2: TX/RX wiring was actually wrong.** On top of the power issue, TX and RX were mixed up between the CP2102 and ESP32-CAM. Because the error message was identical to the power problem, and because of mounting frustration from repeated failed uploads, this got missed for a long time — every failed attempt was blamed on power alone, so the wiring was never double-checked.

**Lesson from this:** when the same error persists after fixing one suspected cause, don't assume the fix didn't work 
## Working: Camera Streaming Correctly

.

## Final Code


## Key Takeaways
- The same error message can have multiple unrelated causes — don't stop investigating once you've found *a* plausible cause if the error persists
- ESP32-CAM's camera + WiFi need meaningfully more current than simple GPIO tests — plan for external 5V power from the start on any camera project
- Frustration after repeated failures makes it easy to tunnel-vision 
- When debugging, isolate variables one at a time — fix power, retest; if it still fails, then re-verify wiring, rather than assuming a fix "should" have worked
