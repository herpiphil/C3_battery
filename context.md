# C3 Battery Test — Project Context

## Last updated: 2026-04-14

## Project Goal
Test LiPo battery life and charging efficiency on an ESP32-C3 SuperMini using an expansion board. All test conditions are controlled from Home Assistant, with sensor data logged for analysis.

## Hardware
- **MCU:** ESP32-C3 SuperMini
- **Expansion board:** Generic SuperMini expansion board with JST battery connector, LiPo charging and protection built in
- **Battery:** LiPo single cell (3.7V nominal)
- **Connection:** OTA (WiFi) for firmware updates. USB port is ttyACM0 or ttyACM1 (changes after reboot)

## Battery Voltage Monitoring
- **Expansion board** has no GPIO-connected battery monitoring pin
- **External voltage divider** added: B+ pad → 100kΩ → GPIO1 (pin 5) → 100kΩ → GND
- **B+ and B- pads** found on back of expansion board PCB
- GPIO0 (pin 4) was tried first but has a 45kΩ internal pull-down (strapping pin) that distorts ADC readings
- GPIO1 (pin 5) confirmed working — no pull-down issue
- **ADC settings:** GPIO1, attenuation 12db, samples: 10
- **Calibration:** multiply: 8.91 (empirical — pin reads 2.001V for 4.002V battery, raw ADC 0.449V)
- **Accuracy:** ~4% (reads 4.15V for actual 4.002V) — acceptable for trend monitoring
- **Stability check:** reading held at 4.151V — monitoring to confirm it holds

## Current Firmware State
- File: `c3_battery_test.yaml`
- ESPHome version: 2026.2.2
- Board definition: `esp32-c3-devkitm-1`
- Framework: Arduino
- API encryption: removed (local network only)
- OTA password: set in secrets.yaml
- Sensor update interval: 10s (shortened for calibration — change to 60s once stable)

## Sensors Reported to Home Assistant
- Battery Voltage (V)
- Battery Level (%) — 0% = 3.0V, 100% = 4.2V
- WiFi Signal (dBm)
- Uptime (s)

## HA Controls
- **Sleep Mode** — Always On / Deep Sleep 30s / 5min / 15min / 1hr (persists across reboots)
- **WiFi Power Save** — Full Power / Light Save / Max Save (persists across reboots)

## Next Steps
1. Confirm voltage reading is stable over time (currently monitoring)
2. Change update_interval back to 60s once stability confirmed
3. Begin battery life tests — switch sleep/WiFi modes in HA and observe discharge rate in history graphs
4. Note: battery level % may need recalibration if voltage range drifts from 3.0-4.2V assumption

## Files
- `c3_battery_test.yaml` — main ESPHome config
- `wifi_ps_helper.h` — header to expose esp_wifi_set_ps() in lambdas
- `secrets.yaml` — WiFi credentials, OTA password (excluded from git)
- `my prefs.txt` — user preference: use ESPHome for compile and flash
