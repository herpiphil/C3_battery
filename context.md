# C3 Battery Test — Project Context

## Last updated: 2026-04-15

## Project Goal
Test LiPo battery life and charging efficiency on an ESP32-C3 SuperMini using an expansion board. All test conditions are controlled from Home Assistant, with sensor data logged for analysis.

## Hardware
- **MCU:** ESP32-C3 SuperMini
- **Expansion board:** Generic SuperMini expansion board with JST battery connector, LiPo charging and protection built in
- **Battery:** LiPo single cell (3.7V nominal)
- **Connection:** OTA (WiFi) for firmware updates. USB port is ttyACM0 or ttyACM1 (changes after reboot)
- **LD2410C:** 24GHz human presence sensor — wired to UART1 (GPIO4/GPIO5) and OUT pin on GPIO3

## Battery Voltage Monitoring
- **Expansion board** has no GPIO-connected battery monitoring pin
- **External voltage divider** added: B+ pad → 100kΩ → GPIO1 → 100kΩ → GND
- **B+ and B- pads** found on back of expansion board PCB
- GPIO0 was tried first but has a 45kΩ internal pull-down (strapping pin) that distorts ADC readings
- GPIO1 confirmed working
- **ADC settings:** GPIO1, attenuation 12db, samples: 10
- **Calibration (2026-04-15):** multiply: 2.02 (pin 2.001V, battery 4.002V, raw ADC 1.979V)
- Previous multiply of 8.91 was from abnormal conditions and has been corrected
- **Confirmed reading:** 3.982V — accurate

## LD2410C Presence Sensor
- **Wiring:** TX→GPIO5, RX→GPIO4, OUT→GPIO3, GND→GND, 5V→5V (expansion board)
- **UART:** 256000 baud, 8N1
- **Sensors in HA:** Presence Detected, Moving Target, Still Target, OUT Pin (binary); Move/Still Distance, Move/Still Energy, Detection Distance (numeric)
- **Status:** Confirmed working — all readings look appropriate

## Current Firmware State
- File: `c3_battery_test.yaml`
- ESPHome version: 2026.2.2
- Board definition: `esp32-c3-devkitm-1`
- Framework: Arduino
- API encryption: removed (local network only)
- OTA password: set in secrets.yaml
- Sensor update interval: 10s (shorten to 60s once stable)

## Sensors Reported to Home Assistant
- Battery Voltage (V)
- Battery Level (%) — 0% = 3.0V, 100% = 4.2V
- WiFi Signal (dBm)
- Uptime (s)
- Presence Detected, Moving Target, Still Target, OUT Pin (binary)
- Move Distance, Still Distance, Move Energy, Still Energy, Detection Distance

## HA Controls
- **Sleep Mode** — Always On / Deep Sleep 30s / 5min / 15min / 1hr (persists across reboots)
- **WiFi Power Save** — Full Power / Light Save / Max Save (persists across reboots)

## Next Steps
1. Monitor voltage reading for stability
2. Change update_interval to 60s once stable
3. Begin battery life tests — switch sleep/WiFi modes in HA and observe discharge rate
4. Note: deep sleep will disconnect the LD2410C — keep Sleep Mode on "Always On" when presence sensing is needed

## Files
- `c3_battery_test.yaml` — main ESPHome config
- `wifi_ps_helper.h` — header to expose esp_wifi_set_ps() in lambdas
- `secrets.yaml` — WiFi credentials, OTA password (excluded from git)
- `my prefs.txt` — user preference: use ESPHome for compile and flash
