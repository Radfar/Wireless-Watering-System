# Wireless-Irrigation-System
                         INTERNET
                            │
                     HTTPS / MQTT over TLS
                            │
                    ┌───────▼────────┐
                    │   Cloud / VPS  │
                    │                │
                    │ MQTT Broker    │
                    │ API Server     │
                    │ Database       │
                    │ Web Application│
                    └───────▲────────┘
                            │
                       Internet
                            │
                    ┌───────▼────────┐
                    │  IoT Gateway   │
                    │                │
                    │                │
                    │ Main Controller│
                    └───────┬────────┘
                              │
          ┌───────────────────┼────────────────────┐
          │                   │                    │
     Valve #1             Valve #2            Valve #100
     ESP8266               ESP8266               ESP8266
          │
       Solenoid

My options for Internet gateway:
Ethernet module	W5500	⭐⭐⭐⭐⭐
Wi-Fi module	ESP8266/ESP32	⭐⭐⭐⭐
4G/LTE modem	SIM7600, A7670, etc.	⭐⭐⭐⭐⭐ for remote fields
Raspberry Pi	Pi + Ethernet/4G	⭐⭐⭐⭐⭐ if you need a powerful gateway
Ethernet + 4G router	Industrial router	⭐⭐⭐⭐⭐ production system

For an industrial/irrigation installation, Ethernet or 4G is preferable to Wi-Fi if you have control over the infrastructure.

Arduino's PubSubClient supports ESP8266 and MQTT 3.1.1.

Main Controller
       │
       │ MQTT
       ▼
mqtt.yourdomain.com
       │
       ├── irrigation/site01/status
       ├── irrigation/site01/valves
       ├── irrigation/site01/flow
       ├── irrigation/site01/tank
       └── irrigation/site01/alarm
main controller:
                 ┌─────────────────────┐
                 │    Main Controller  │
                 │                     │
                 │ Local control       │
                 │ Valve management    │
                 │ Flow measurement    │
                 │ Tank measurement    │
                 │ Alarm management    │
                 │ SMS                 │
                 │                     │
                 │ MQTT Gateway        │◄──── Internet
                 └─────────────────────┘
if the Internet disappears, irrigation should continue working.
Internet OFF
     ↓
Cloud unavailable
     ↓
Main controller continues operating
     ↓
Valves continue working
     ↓
Flow/tank protection continues
     ↓
Internet returns
     ↓
Controller reconnects
     ↓
Synchronizes status

#Using MQTT for commands AND telemetry

For example, the web application could send:

irrigation/site01/command

with:

{
  "command": "valve_on",
  "valve": 17,
  "duration": 600
}

The controller receives it and operates valve 17.

Then it publishes:

irrigation/site01/valve/17/status
{
  "state": "ON",
  "remaining": 542
}

And periodically:

irrigation/site01/telemetry
{
  "flow_lpm": 32.4,
  "pressure_bar": 3.1,
  "tank_percent": 74,
  "active_valves": 3
}

