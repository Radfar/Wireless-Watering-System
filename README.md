# Wireless Watering System

An offline-first wireless irrigation controller. The current controller firmware is an Arduino UNO sketch in [`src/main.ino`](src/main.ino), which sends 433 MHz RF commands to ESP8266-based remote valve nodes. Each node drives a 6 V DC bi-stable (latching) valve through an appropriate driver circuit.

> **Current scope:** local watering control and RF valve operation. MQTT, soil-moisture telemetry, Internet access, and cloud integration are later development stages and are not required for the current watering sequence.

## Current architecture

```text
Arduino UNO controller
  - local interface and EEPROM settings
  - RF transmitter / pump output / SIM800 interface
                 |
           433 MHz RF
                 |
         ESP8266 WEMOS D1
                 |
           valve driver
                 |
        6 V DC bi-stable valve
```

The controller sketch currently defines four valve IDs (0–3). Actual deployment count and RF coverage must be validated against the physical installation. RF repeaters are not confirmed as implemented in the current controller sketch.

## Controller details from the current source

| Item | Current value |
|---|---|
| Main controller | Arduino UNO |
| RF library | RCSwitch |
| RF transmitter signal | Digital pin 12 |
| Pump/main-valve relay output | Digital pin 13 |
| SIM800 interface | SoftwareSerial RX 10, TX 11 |
| LCD | 16 × 2 LiquidCrystal |
| Button ladder | Analog input A0 |
| Settings | EEPROM |
| Valve IDs | 0–3 (`VALVE = 4`) |
| Open / ON code | `2560 + valve_id` |
| Close / OFF code | `1280 + valve_id` |
| RF command width | 24 bits; each command sent twice |
| Watering delay constant | `WATERING_DURATION = 2000` ms |

**Commissioning note:** the source comment says the watering delay is one minute, but the configured value is 2,000 ms (2 seconds). Confirm the intended duration before live irrigation deployment.

## Valve node

The ESP8266 valve firmware is maintained in the [Wireless Valve Node repository](https://github.com/Radfar/wireless-valve-node). The current node source listens for the assigned RF command and generates a short directional pulse through an external driver. Verify driver topology, coil current, pulse polarity, and pulse duration against the valve and driver specifications.

## RF command mapping

| Action | Decimal code for valve ID `n` |
|---|---:|
| Open / ON | `2560 + n` |
| Close / OFF | `1280 + n` |

This fixed-code protocol does not provide acknowledgement, encryption, sender authentication, or proof that a valve physically moved.

## Future development — separate from current control

- ESP8266/ESP32 IoT gateway
- MQTT telemetry and remote commands
- Soil-moisture monitoring
- Web dashboard and cloud integration

Keep the MQTT soil-moisture experiment separate from the active RF valve firmware. The local watering system should not depend on Internet availability.

## Safety and commissioning

- Do not connect a valve coil directly to an ESP8266 GPIO; use a driver rated for the valve's pulse current and inductive load.
- Verify valve voltage, polarity, and maximum pulse duration from the manufacturer's datasheet.
- Confirm pump relay ratings and appropriate electrical isolation/protection.
- Do not rely on commented-out protection code as an active safety function.
- Test RF reception and physical valve movement separately; a transmitted code is not proof of movement.

## Development status

This repository documents a prototype, not product certification or guaranteed field reliability. Record test results, hardware revisions, wiring diagrams, and known limitations before customer handover.

## License

No license has been specified. Add a license before redistributing or commercially reusing the source.

