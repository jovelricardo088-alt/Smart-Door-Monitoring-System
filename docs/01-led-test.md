# Stage 1: LED Blink Test — Confirming the ESP32-CAM Works

**Goal:** Before writing any door-monitoring logic, confirm the ESP32-CAM board, CP2102 programmer, and upload process all work correctly using the simplest possible test — blinking an external LED.

## Hardware
- AI Thinker ESP32-CAM
- CP2102 USB to TTL (UART) converter
- Breadboard, 1x LED, 1x 220–330Ω resistor, jumper wires

## Wiring
| CP2102 | ESP32-CAM |
|---|---|
| TX | RX (U0R) |
| RX | TX (U0T) |
| GND | GND |
| 5V | 5V |

LED: anode (long leg) → resistor → GPIO14, cathode (short leg) → GND.
GPIO0 → GND, connected only while flashing new code.

---

## Problem 1: Driver Confusion
Installed the wrong driver initially — mixed up the ESP32 board driver with the CP2102 programmer's driver. The CP2102 needed the **Silicon Labs CP210x VCP driver**. A reboot after installing was required before Windows recognized the device correctly.

## Problem 2: "No Serial Data Received"
Uploading failed repeatedly with this error:

> A fatal error occurred: Failed to connect to ESP32: No serial data received.

  ![No serial data received error](../images/led-test/no-serial-data.jpg)

**Cause:** The ESP32-CAM has no auto-reset circuit, so it has to be put into bootloader mode manually:
1. GPIO0 → GND *before* clicking Upload
2. Click Upload
3. The instant the console shows `Connecting.......`, press and release the board's RESET button
4. Upload proceeds

## Problem 3: LED Stuck Solid, Not Blinking
After finally getting an upload to succeed (`Hard resetting via RTS pin...`), the LED lit up but stayed **solid on** instead of blinking every 2 seconds.

<video src="../images/01-led-test/led-stuck-solid.mp4" controls width="500"></video>

*Add video here of the LED staying solid/not blinking. (See note on video embedding below.)*

**Two causes found:**
- **GPIO0 was still grounded** after upload — the board was sitting in bootloader mode, not running the program. Fix: disconnect GPIO0 from GND, then press RESET again so it boots into run mode.
- **GPIO12 is a strapping pin** — wiring the LED to it interfered with the board's boot behavior. Moved the LED to **GPIO14**, a safe general-purpose pin.
- A loose breadboard rail connection on the GPIO0 ground wire was also causing inconsistent behavior — wiring GPIO0 directly to a GND pin (instead of through the rail) made it reliable.

## Working: LED Blinking Correctly

<video src="../images/01-led-test/led-blinking-success.mp4" controls width="500"></video>

*Add video here of the LED blinking on/off every 2 seconds.*

## Final Code
```cpp
int Led = 14;

void setup() {
  pinMode(Led, OUTPUT);
}

void loop() {
  digitalWrite(Led, HIGH);
  delay(2000);
  digitalWrite(Led, LOW);
  delay(2000);
}
```
Full file: [`firmware/01_led_blink/led_blink.ino`](../firmware/01_led_blink/led_blink.ino)

## Key Takeaways
- CP2102 driver ≠ ESP32 driver — install the Silicon Labs one
- ESP32-CAM needs manual GPIO0/reset flashing steps every time (no auto-reset)
- After upload: disconnect GPIO0, reset again, or code won't run
- Avoid GPIO12 for general I/O — it's a boot strapping pin
- Loose breadboard rail connections cause intermittent, confusing failures — when it works sometimes and fails other times, suspect the breadboard first

---
*Next: [Stage 2 — Camera Test](02-camera-test.md)*

