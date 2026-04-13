# C3 Battery Test — Project Context

## Last updated: 2026-04-14

## Project Goal
Test LiPo battery life and charging efficiency on an ESP32-C3 SuperMini using an expansion board. All test conditions are controlled from Home Assistant, with sensor data logged for analysis.

## Hardware
- **MCU:** ESP32-C3 SuperMini
- **Expansion board:** Generic SuperMini expansion board with JST battery connector, LiPo charging and protection built in
- **Battery:** LiPo (single cell, 3.7V nominal) — not yet identified/specified
- **Connection:** USB (dev/ttyACM0 or ttyACM1 — port number can change after reboot)

## Current Firmware State
- File: `c3_battery_test.yaml`
- Flashed successfully via USB on 2026-04-14
- ESPHome version: 2026.2.2
- Board definition: `esp32-c3-devkitm-1`
- Framework: Arduino

## Battery ADC
- **Assumed pin:** GPIO1 — NOT yet confirmed against board schematic
- **Assumed divider:** 1:1 (100k/100k), multiply factor = 2.0
- **Status:** Unconfirmed. Battery was connected showing 2.776V — possibly flat battery, possibly wrong divider ratio. Need to charge and verify reading climbs toward 4.2V.

## Sensors Reported to Home Assistant
- Battery Voltage (V)
- Battery Level (%)  — 0% = 3.0V, 100% = 4.2V
- WiFi Signal (dBm)
- Uptime (s)

## HA Controls
- **Sleep Mode** — Always On / Deep Sleep 30s / 5min / 15min / 1hr (persists across reboots)
- **WiFi Power Save** — Full Power / Light Save / Max Save (persists across reboots)

## API / Security
- API encryption removed for simplicity (local network only)
- OTA password set in secrets.yaml

## Known Issues / Next Steps
1. Confirm battery ADC GPIO pin — charge battery and watch if voltage climbs to ~4.2V
2. If voltage tops out below 4.2V, adjust `multiply` factor in YAML to match actual divider ratio
3. Once voltage reading is confirmed correct, begin battery life tests by switching sleep/WiFi modes in HA
4. Consider adding a multimeter check of actual battery voltage to calibrate the ADC reading

## Files
- `c3_battery_test.yaml` — main ESPHome config
- `wifi_ps_helper.h` — header to expose esp_wifi_set_ps() in lambdas
- `secrets.yaml` — WiFi credentials, OTA password (excluded from git)
- `my prefs.txt` — user preference: use ESPHome for compile and flash
