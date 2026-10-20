# Wearable Telemetry Chain — nRF52840 BLE → ESP32 → Wi-Fi → Live Dashboard

[![nRF52840](https://img.shields.io/badge/MCU-nRF52840-00A9CE)](https://www.nordicsemi.com/Products/nRF52840)
[![ESP32](https://img.shields.io/badge/MCU-ESP32-E7352C)](https://www.espressif.com/en/products/socs/esp32)
[![BLE](https://img.shields.io/badge/link-BLE%20%7C%20UART%20%7C%20WebSocket-555)](#how-it-works)

A body-worn sensor node that streams motion and physiological data to a browser in real time through four hops. An nRF52840 board reads an IMU, an ECG front end, a load cell and a pulse-oximetry sensor and publishes them as BLE characteristics; an ESP32 gateway subscribes, converts to JSON and passes it over UART to a second ESP32, which forwards it over Wi-Fi and WebSockets to a Python hub and a live dashboard.

Built in 2026.

## How it works

```
nRF52840 (LSM6DS3 IMU · ECG · HX711 load cell · MAX30105 SpO2)
   │ BLE notify (one characteristic per sensor)
   ▼
ESP32 #1  BLE client → JSON  ──UART──▶  ESP32 #2  Wi-Fi + WebSocket
                                              │
                                              ▼
                                  server.py (Python hub) → dashboard.html
```

## Key features

- Four sensors on one BLE service, each on its own characteristic.
- Sensor start-up ordered on purpose: one sensor library changes the I²C bus clock, so initialization order matters.
- DC-DC power mode enabled on the nRF52840 to save energy.
- Two-stage gateway: BLE on one ESP32, Wi-Fi on another, joined by UART.

## Files

`wearables.ino` sensor node (nRF52840) · `blercv-uart-trans.ino` BLE → UART gateway · `uartrcv-wifi.ino` UART → Wi-Fi/WebSocket · `server.py` hub · `dashboard.html` live view

For `uartrcv-wifi.ino`, copy `secrets.h.example` to `secrets.h` and set your Wi-Fi name, password and the hub's IP address; `secrets.h` is git-ignored.

## Known quirk

The node's IMU format differs from what the hub expects; a regular expression in `server.py` compensates.

## Related

[ESP32 EEG recorder](https://github.com/Curious-Utkarsh/EEG_UDL) · [High-rate vibration DAQ](https://github.com/Curious-Utkarsh/Teensy_Esp)

---
[Kumar Utkarsh](https://github.com/Curious-Utkarsh) · [LinkedIn](https://www.linkedin.com/in/kumar-utkarsh-124aab227/)
