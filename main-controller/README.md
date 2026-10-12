# Wireless Watering System

A wireless irrigation control system based on an Arduino Mega 2560 main controller, ESP8266-based valve nodes, RF communication, RF repeaters, and GSM/SMS communication.

The project is designed for irrigation installations where installing dedicated control wiring between a central controller and distributed water valves is impractical.

The system provides local autonomous irrigation control and is being developed toward an IoT-enabled architecture that can expose real-time data and control functions to external applications and SCADA/BAS platforms.

> **Project status:** Hardware and embedded-system development.  
> SCADA/HMI development is maintained separately in the `building-irrigation-scada` repository.

---

## Project Goals

The primary goals of this project are:

- Wireless control of distributed irrigation valves
- Local and autonomous irrigation operation
- Support for a large number of valve nodes
- Extended RF communication using repeaters
- Monitoring of irrigation process variables
- GSM/SMS communication for notifications and remote functions
- Reliable operation without permanent Internet connectivity
- Future connectivity to IoT platforms and industrial SCADA/BAS systems

The design intentionally separates **local physical control** from **Internet, cloud, and SCADA functions**.

---

# System Architecture

The current system consists of four main layers:

```text
                    ┌──────────────────────────┐
                    │    SCADA / IoT / Cloud   │
                    │                          │
                    │ Future integration layer │
                    └────────────┬─────────────┘
                                 │
                           Gateway / API
                                 │
                    ┌────────────▼─────────────┐
                    │    Main Controller       │
                    │      Arduino Mega 2560   │
                    │                           │
                    │ • Irrigation logic       │
                    │ • Scheduling              │
                    │ • Valve management        │
                    │ • Pump control            │
                    │ • Sensors                │
                    │ • RF communication       │
                    │ • GSM/SMS                 │
                    └────────────┬─────────────┘
                                 │
                              RF Network
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
       ┌──────▼──────┐    ┌──────▼──────┐    ┌──────▼──────┐
       │ RF Repeater │    │ Valve Node  │    │ Valve Node  │
       │             │    │  ESP8266    │    │  ESP8266    │
       └──────┬──────┘    └──────┬──────┘    └──────┬──────┘
              │                  │                  │
         RF extension       Solenoid Valve     Solenoid Valve
