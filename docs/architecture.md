# System Architecture

## Current topology

The current watering prototype consists of an Arduino UNO main controller and one or more ESP8266 WEMOS D1 valve nodes. The controller sends RF commands in the 433 MHz band; each node listens for commands matching its compiled valve ID and pulses a driver for a 6 V DC bi-stable valve.

```text
Arduino UNO
  | local watering logic, LCD/buttons, EEPROM, pump output
  | 433 MHz RF transmitter (RCSwitch)
  v
ESP8266 WEMOS D1 valve node
  | RF receiver (RCSwitch)
  v
Valve driver
  | directional pulse
  v
6 V DC bi-stable valve
```

## Responsibility split

- **Arduino UNO:** local user interface, stored watering parameters, RF command transmission, pump relay output, and SIM800 serial interface in the current sketch.
- **ESP8266 valve node:** RF command reception and valve-driver output pulses.
- **IoT gateway (future):** network connectivity, MQTT telemetry, and remote supervisory functions. It is not part of the current valve command path.

## Boundaries and assumptions

- The controller source currently defines four valve IDs (0–3).
- The node source currently defines `VAL_NO = 1`.
- The RF protocol sends commands but does not confirm physical valve state.
- Exact RF module models, antenna arrangement, driver schematic, power supply ratings, and field range must be documented from the physical build.
- This diagram describes the intended architecture; it is not evidence that all features have passed system testing.
