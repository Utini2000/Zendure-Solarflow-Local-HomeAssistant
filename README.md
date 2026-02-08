# What is it
This Home Assistant package provides advanced, local control for the Zendure SolarFlow system (optimized for Hyper 2000 and 800 Pro models). 

It focuses on maximizing battery lifespan and achieving near-perfect zero-export by leveraging local communication and smart balancing between two units.

Also compatible with only one unit.

Completely local, no internet access required.

# Key Features
Smart Dual-Unit Balancing: Dynamically distributes load between two units based on their State of Charge (SoC) and current Solar (PV) input to ensure batteries drain and charge evenly.

Seasonal Logic: Automatically switches between Summer (discharge down to 8%) and Winter (discharge down to 20%) profiles to protect battery chemistry during cold months.

Comprehensive Battery Protection: * Cold-Charge Protection: Throttles charging power when temperatures drop below 10°C.

Heat Protection: Lowers thresholds to 45°C for earlier bypass action.

Soft-Landing: Throttles power at >90% SoC to improve cell balancing.

Hardware Longevity (Flash Guard): Uses smartMode: 1 to write settings to RAM instead of Flash memory, preventing the internal storage from wearing out due to frequent updates.

Intelligent Calibration: Performs automatic top-balancing every 14 days or when a "voltage sag" is detected, ensuring the BMS remains accurate.

Self-Healing & Failsafe: * Anti-Drift Watchdog: Automatically resyncs if internal memory and actual device state diverge.

Shelly Offline Mode: If the grid meter fails, the system falls back to a safe "base load" output.

Ghost Load Protection: If one unit goes offline, the other is prevented from over-compensating and exceeding legal limits.

Detailed Analytics: Provides precise "Net Flow Logic" sensors for the Home Assistant Energy Dashboard, correctly separating charging from discharging even if the API reports noisy data.


# Performance Targets
Grid Optimization: Fixed 20W Grid Bias (import target) and 20W Hysteresis.

The Result: A rock-solid 1–15W constant consumption with virtually zero accidental grid export, while ignoring small "noise" (like LED flickers) to reduce network traffic.

Solar Spillover: When batteries hit 98% and solar power is high (>400W), the system forces an output of 350W+ to ensure solar energy is utilized rather than wasted.


# Prerequisites
Home Assistant (HA)

Shelly 3EM Pro

Shelly 3EM Pro Total Active Power entity (see energy_monitoring.yaml as an example)

HA Integration: Mosquitto Broker

MQTT configured for each SolarFlow in the app to the local HA MQTT IP address

Optional: HA Integration Forecast.Solar

Optional: Telegram Bot for notifications in Telegram


# Installation
I am using this .yaml in /packages/ folder and zendure_control.yaml

Please adapt the following placeholder in the script


# Configuration Placeholders
The following placeholders must be adjusted:

HUB1_SERIAL_NUMBER: Serial number of the 1st SolarFlow Hub (e.g., EEA1...).

BAT1_SERIAL_NUMBER: Serial number of the battery on the 1st Hub (e.g., CO4...).

HUB1_IP_ADDRESS: Local IP address of the 1st Hub (e.g., 192.168.178.50).

HUB2_SERIAL_NUMBER: Serial number of the 2nd SolarFlow Hub.

BAT2_SERIAL_NUMBER: Serial number of the battery on the 2nd Hub.

HUB2_IP_ADDRESS: Local IP address of the 2nd Hub.

shellypro3em_SHELLY_ID: Unique ID of the Shelly 3EM (the part following shellypro3em_ in the entity ID).

notify.YOUR_TELEGRAM_SERVICE: Name of the notification service (Telegram, mobile app, etc.).
