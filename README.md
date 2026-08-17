# Smart Door Monitoring System

An ESP32-based door monitoring and security system designed to detect when a door is opened and provide an alert if it remains open for a specified amount of time.

## Project Overview

The Smart Door Monitoring System uses a magnetic reed switch to monitor the state of a door. When the door is opened, the system detects the change in state and begins monitoring the amount of time the door remains open.

If the door remains open beyond the programmed time limit, the system activates an audible and visual alert.

The system is designed to be compact enough to be mounted near the top of a door and can be expanded with additional sensors and Wi-Fi functionality.

## Goals

* Detect whether a door is open or closed
* Provide an alert when the door remains open too long
* Create a compact and practical security device
* Use an ESP32 for future Wi-Fi-based functionality
* Design a system that can eventually be placed inside a custom enclosure

## Features

* Magnetic reed switch for door detection
* ESP32 microcontroller
* LED status indicator
* Buzzer alarm
* Programmable door-open timer
* Wi-Fi-capable microcontroller for future expansion
* Potential integration with additional sensors

## How It Works

1. A magnet is attached to the door near the magnetic reed switch.
2. When the door is closed, the magnet is positioned close to the reed switch.
3. Opening the door moves the magnet away from the reed switch.
4. The ESP32 detects the change in the reed switch state.
5. The system begins timing how long the door remains open.
6. If the door stays open longer than the programmed limit, the LED and buzzer activate.
7. When the door closes again, the system resets.

## System Components

| Component            | Purpose                                           |
| -------------------- | ------------------------------------------------- |
| ESP32                | Main microcontroller                              |
| Magnetic Reed Switch | Detects whether the door is open or closed        |
| Magnet               | Activates the reed switch when the door is closed |
| LED                  | Visual status/alert indicator                     |
| Buzzer               | Audible alert                                     |
| Wires                | Electrical connections                            |
| Power Source         | Powers the system                                 |

## System Diagram

*System diagram will be added as the project develops.*

## Pin Connections

| Component   | ESP32 Pin |
| ----------- | --------- |
| Reed Switch | TBD       |
| LED         | TBD       |
| Buzzer      | TBD       |

The final pin assignments will be documented after the hardware configuration is finalized.

## Software

The project is programmed using the Arduino IDE.

The main program is located in:

```text
src/door_monitor.ino
```

The software is responsible for:

* Reading the reed switch
* Determining the door state
* Tracking how long the door remains open
* Controlling the LED
* Controlling the buzzer
* Resetting the system when the door closes

## Testing

Testing will be performed throughout the development process.

| Test                    | Expected Result           | Status      |
| ----------------------- | ------------------------- | ----------- |
| Door closed             | System remains inactive   | In Progress |
| Door opened             | System detects open state | In Progress |
| Door remains open       | Timer continues counting  | In Progress |
| Door exceeds time limit | LED and buzzer activate   | In Progress |
| Door closes             | System resets             | In Progress |

Testing results will be updated as the system is developed.

## Future Improvements

Potential future versions of the system may include:

* Wi-Fi notifications
* Web-based monitoring
* Mobile notifications
* Camera integration
* Additional magnetic sensors
* Motion detection
* Battery monitoring
* Custom 3D-printed enclosure
* Remote monitoring of door status

## Project Development

This project is being developed as an engineering project, with documentation of the design process, hardware development, programming, testing, and future improvements.

The repository will be updated as new versions of the system are developed.

## License

This project is intended for educational and engineering purposes.

