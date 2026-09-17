# Stage 3: Reed Switch + Telegram Notifications

**Goal:** Detect the door opening with a magnetic reed switch and push a notification to my phone via a Telegram bot.

---

## Hardware

- AI Thinker ESP32-CAM
- Magnetic reed switch
- LED (status indicator)
- Jumper wires

---

## How the Reed Switch Works

Wired with `INPUT_PULLUP`:

- Magnet **near** (door closed) → switch closed → pin reads **LOW**
- Magnet **away** (door open) → switch open → pin reads **HIGH**

Wiring: one leg of the reed switch → GPIO13, other leg → GND. (Avoided GPIO12 since it's a boot strapping pin — learned that the hard way in an earlier stage.)

---

## Problem 1: `UniversalTelegramBot.h: No such file or directory`

The library wasn't installed. Fixed via **Sketch → Include Library → Manage Libraries**, searching for **UniversalTelegramBot** (by Brian Lough) and installing it, along with its **ArduinoJson** dependency. A full IDE restart was needed afterward for it to be recognized.

---

## Problem 2: Bot ID vs Chat ID Confusion

Initially used the **bot's own ID** as the chat ID. That didn't work — Telegram needs to know *where* to deliver the message (my chat with the bot), not who the sender is.


Also learned the difference between defining the constant and using it:

```cpp
#define CHAT_ID "123456789"   // the real number goes here, once, as a string

bot.sendMessage(CHAT_ID, "Door opened!", "");  // just reference the name here
```

---

## Problem 3: No Message Arriving

Debugging steps that helped narrow this down:

- **`client.setInsecure()`** — Telegram's API is HTTPS, and the ESP32 will refuse the connection without this line. Easy to miss, and it fails silently with no error.
- **Boot-time test message** — added `bot.sendMessage(CHAT_ID, "Bot online!", "")` at the end of `setup()`. If it arrives, the token/chat ID/WiFi are all fine and the issue is in the sensor logic. If it doesn't, the sensor is irrelevant and the problem is in the connection layer.
- **Checking the return value** — `sendMessage()` returns a bool, so printing it to Serial shows whether the call is being reached and whether Telegram accepted it.
- **Watching the LED** — if the LED responds to the magnet but no message arrives, the sensor logic is confirmed working and the problem is purely on the Telegram side.

*(I fixed the setup and loop logic)*

---

## Logic

The notification only fires on the **transition** from closed to open, not continuously while the door stays open. A timer also means the door has to stay open for a set duration before it triggers, which avoids false alerts from brief openings.

```cpp
void loop() {
  bool sensorActive = digitalRead(reedPin); // HIGH = magnet away

  if (sensorActive) {
    if (!timing) {           // only start the stopwatch once, on the transition
      timing = true;
      sensorHigh = millis();
    }
    if (!outputState && (millis() - sensorHigh >= delayTime)) {
      outputState = true;
      bot.sendMessage(CHAT_ID, "Door opened!", "");
      digitalWrite(ledPin, HIGH);
    }
  } else {
    timing = false;
    outputState = false;
    digitalWrite(ledPin, LOW);
  }

  delay(50); // small debounce
}
```

Full file: `firmware/03_reed_telegram/reed_telegram.ino`

---

## Key Takeaways

- "Which ID?" matters — the bot token identifies the *sender*, the chat ID identifies the *destination*
- `client.setInsecure()` is required for HTTPS calls to Telegram and fails quietly without it
- A test message in `setup()` is the fastest way to split a problem into "connection issue" vs "logic issue"
- Tracking state between loops (`lastState` / `timing` / `outputState`) is what turns a continuous sensor reading into a single discrete event

---

## Planned Additions

- **PIR motion sensor** — detect someone approaching before the door opens, and trigger the camera to confirm whether it's a package delivery or someone recognized
- **Temperature sensor** — get the exact outdoor temperature at the house instead of a regional weather app average
- **Enclosure** — a proper housing for the electronics, with clear line of sight for the camera/PIR and clean wiring to the door frame
