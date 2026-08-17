# Wireless Watering System

A wireless irrigation and watering control system built around an **Arduino Mega 2560 main controller** and distributed **ESP8266-based valve controllers**.

The system was originally developed for garden and irrigation applications where running control wiring between valves is difficult. Valve commands are transmitted wirelessly, while the main controller manages watering schedules, the pump, system status, and cellular SMS communication.

> **Project status:** Existing wireless irrigation controller. IoT/cloud connectivity is currently under development.

---

## System Architecture

```text
                         ┌──────────────────────┐
                         │      Web / Cloud      │
                         │   IoT Application     │
                         └──────────┬───────────┘
                                    │
                              MQTT / HTTPS
                                    │
                           Future IoT Gateway
                                    │
                                    │
                         ┌──────────▼──────────┐
                         │   Arduino Mega 2560 │
                         │    Main Controller   │
                         │                      │
                         │ • Watering schedules │
                         │ • Valve management   │
                         │ • Pump control       │
                         │ • Sensors            │
                         │ • SMS communication  │
                         │ • RF communication   │
                         └──────────┬───────────┘
                                    │
                               RF Wireless
                                    │
               ┌────────────────────┼────────────────────┐
               │                    │                    │
        ┌──────▼──────┐      ┌──────▼──────┐      ┌──────▼──────┐
        │ Valve Node  │      │ Valve Node  │      │ Valve Node  │
        │  ESP8266    │      │  ESP8266    │      │  ESP8266    │
        └──────┬──────┘      └──────┬──────┘      └──────┬──────┘
               │                    │                    │
          Solenoid Valve       Solenoid Valve       Solenoid Valve
```

The system can be expanded to a large number of wireless valve nodes. RF repeaters can be used where the physical distance between the main controller and valve nodes exceeds the reliable communication range.

---

## Main Controller

The main controller is based on an **Arduino Mega 2560**.

Its responsibilities include:

* Managing watering schedules
* Controlling multiple wireless valves
* Controlling the main pump/relay
* Storing configuration in EEPROM
* Providing a local LCD user interface
* RF wireless communication
* Monitoring system conditions
* Sending SMS notifications through a SIM800 module
* Executing irrigation locally without requiring an Internet connection

The controller is designed so that the core irrigation process can continue operating independently of any future cloud or Internet connection.

---

## Wireless Valve Controllers

Each remote valve controller is based on an **ESP8266**.

A valve node is responsible for controlling its local irrigation valve and communicating with the main controller through the wireless network.

The distributed architecture avoids the need to install long control cables throughout a garden or irrigation area.

```text
Main Controller
       │
       │ RF
       ▼
   Valve Node
   ESP8266
       │
       ▼
 Solenoid Valve
```

The number of deployed valve nodes can be increased according to the requirements of the irrigation installation.

---

## RF Communication and Repeaters

The current system uses RF communication between the Arduino Mega 2560 and the remote valve controllers.

For installations where direct communication is not reliable because of distance, physical obstacles, or the layout of the garden, **RF repeaters** can be deployed to extend the communication range.

```text
Main Controller
       │
       │ RF
       ▼
    Repeater
       │
       │ RF
       ▼
  Remote Valve
```

The RF network is currently the primary wireless control mechanism for the irrigation system.

---

## SIM800 Cellular Communication

The main controller includes a **SIM800 GSM module**.

The cellular interface provides SMS-based communication and can be used for system notifications and remote communication where appropriate.

The cellular communication is independent from the local RF valve network.

This provides an additional communication path for installations where Internet connectivity is unavailable.

---

## Local Operation

One of the main design principles of the system is **local control**.

The irrigation controller does not depend on a cloud server for basic watering operation.

```text
Internet unavailable
        │
        ▼
Arduino continues operating
        │
        ├── Watering schedules
        ├── Valve control
        ├── Pump control
        └── Local protection
```

Future Internet connectivity will be added as a supervisory and remote-control layer rather than making the cloud responsible for the basic real-time irrigation process.

---

## Planned IoT Architecture

The next development stage is to connect the existing controller to a web-based IoT application.

The planned architecture is:

```text
                     Internet / Cloud
                            │
                       MQTT / HTTPS
                            │
                  ┌─────────▼─────────┐
                  │    IoT Gateway    │
                  │   ESP8266/ESP32   │
                  └─────────┬─────────┘
                            │ UART
                  ┌─────────▼─────────┐
                  │   Arduino Mega    │
                  │   Main Controller │
                  └─────────┬─────────┘
                            │
                           RF
                            │
                  Wireless Valve Network
```

The gateway will provide network connectivity while the Arduino Mega continues to handle the local irrigation logic.

This separation is intentional:

* **Arduino Mega:** real-time control and local automation
* **RF network:** wireless valve communication
* **ESP8266/ESP32 gateway:** Internet connectivity
* **MQTT:** IoT messaging
* **Web application:** monitoring, visualization, configuration, and remote commands

---

## Planned MQTT Communication

MQTT is being considered as the primary communication protocol between the irrigation controller and the IoT platform.

Example topics:

```text
site/{site_id}/status
site/{site_id}/telemetry
site/{site_id}/command
site/{site_id}/events

site/{site_id}/valves/{valve_id}/status
```

Example telemetry:

```json
{
  "flow_lpm": 32.4,
  "pressure_bar": 3.1,
  "tank_percent": 74,
  "active_valves": 3,
  "pump": true
}
```

Example remote command:

```json
{
  "command": "valve_on",
  "valve": 17,
  "duration": 600
}
```

The final communication protocol and data model are still under development.

---

## Future Sensor Integration

The architecture is intended to support additional irrigation measurements, including:

* Water flow
* Pipe pressure
* Tank level
* Pump status
* Valve status
* Battery status of remote nodes
* Communication status
* System alarms

These measurements will eventually be available to the web application for monitoring and historical analysis.

---

## Reliability and Safety

Because the system controls physical irrigation equipment, network connectivity should not be required for basic safety functions.

Future versions will focus on:

* Local autonomous operation
* Communication acknowledgements
* Command retries
* Valve communication timeout detection
* Maximum valve operating time
* Flow monitoring
* Tank-level protection
* Pump protection
* Communication failure detection
* Watchdog and automatic recovery
* Remote firmware updates
* Secure MQTT communication

Critical protection logic should remain on the local controller rather than relying on the cloud.

---

## Repository Structure

The current repository contains the Arduino-based main controller firmware.

```text
Wireless-Watering-System/
│
├── README.md
│
└── WIS_UI_V7.0_RE.ino
```

Additional firmware and gateway components will be added as the IoT architecture evolves.

---

## Current Development Status

### Existing

* [x] Arduino Mega-based main controller
* [x] Wireless RF valve control
* [x] ESP8266 valve controllers
* [x] RF repeaters for extended range
* [x] Local watering schedules
* [x] EEPROM configuration storage
* [x] LCD user interface
* [x] Pump control
* [x] SIM800 GSM/SMS communication

### In Development

* [ ] IoT gateway
* [ ] Wi-Fi connectivity
* [ ] MQTT communication
* [ ] Cloud/backend integration
* [ ] Real-time web monitoring
* [ ] Remote valve control
* [ ] Telemetry collection
* [ ] Alarm and event management
* [ ] Remote configuration
* [ ] OTA firmware updates
* [ ] Secure device authentication

---

## Design Philosophy

The system follows a **local-first IoT architecture**:

> **The irrigation system should continue to operate even when the Internet is unavailable.**

Internet connectivity is intended to provide remote monitoring, configuration, telemetry, and supervisory control without making the physical irrigation process dependent on the cloud.

This approach allows the existing wireless irrigation system to evolve into a scalable IoT platform while preserving the reliability of the original local controller.

---

## License

This project is currently under development. Licensing information will be added when the project reaches a stable release.
