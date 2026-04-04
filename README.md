# What is it
This Home Assistant package provides **advanced, local control** for the Zendure SolarFlow system — optimized for **Hyper 2000** and **800 Pro** models.

It focuses on maximizing battery lifespan and achieving near-perfect zero‑export by leveraging local communication and smart balancing between two units.

✅ Also compatible with a **single unit**.  
🔒 **Completely local** - no internet access required.  
✅ Works with **any powermeter** that measures the total active power consumption in real time (Shelly 3EM, Shelly 3EM Pro, Zendure Smart Meter 3CT,...).

# Key Features
- **Smart Dual‑Unit Balancing:** Dynamically distributes load between two units based on their State of Charge (SoC) and current solar (PV) input to ensure batteries drain and charge evenly.
- **Seasonal Logic:**  Automatically switches between **Summer** (discharge down to 8%) and **Winter** (discharge down to 20%) profiles to protect battery chemistry during cold months.
- **Comprehensive Battery Protection**
  - **Cold‑Charge Protection:** Throttles charging power when temperatures drop below 10°C
  - **Heat Protection:** Lowers thresholds to 45°C for earlier bypass action
  - **Soft‑Landing:** Throttles power at >90% SoC to improve cell balancing
- **Hardware Longevity (Flash Guard):** Uses `smartMode: 1` to write settings to **RAM instead of Flash memory**, preventing the internal storage from wearing out due to frequent updates.
- **Intelligent Calibration:** Performs automatic top‑balancing every **14 days** or when a **voltage sag** is detected, ensuring the BMS remains accurate.
- **Self‑Healing & Failsafe**
  - **Anti‑Drift Watchdog:** Automatically resyncs if internal memory and actual device state diverge
  - **Shelly Offline Mode:** If the grid meter fails, the system falls back to a safe *base load* output
  - **Ghost Load Protection:** If one unit goes offline, the other is prevented from over‑compensating and exceeding legal limits
- **Detailed Analytics:** Provides precise *Net Flow Logic* sensors for the Home Assistant Energy Dashboard, correctly separating charging from discharging — even if the API reports noisy data.


# Performance Targets
- **Grid Optimization:** Fixed 20W Grid Bias (import target) and 20W Hysteresis.
- **The Result:** A rock-solid 1–15W constant consumption with virtually zero accidental grid export, while ignoring small "noise" (like LED flickers) to reduce network traffic.
- **Solar Spillover:** When batteries hit 98% and solar power is high (>400W), the system forces an output of 350W+ to ensure solar energy is utilized rather than wasted.


# Prerequisites
* Home Assistant (HA)
* Power Meter e.g. Shelly 3EM Pro or Zendure Smart Meter 3CT or similiar
* Home Assistant App: [Mosquitto broker](https://github.com/home-assistant/addons/blob/master/mosquitto/DOCS.md)
* MQTT configured for each SolarFlow in the app to the local HA MQTT IP address
* Optional: Shelly 3EM Pro Total Active Power entity (see [energy_monitoring.yaml](energy_monitoring.yaml) as an example)
* Optional: HA Integration [Forecast.Solar](https://www.home-assistant.io/integrations/forecast_solar/)
* Optional: HA Integration [Notifications](https://www.home-assistant.io/integrations/notify/), e.g. Telegram Bot for notifications


# Installation

1. Configure in the Zendure App
- Set the Zendure to **Base Load Mode** with **0 watts**
- Set **BMS minimum discharge** to **5%**
- **Remove** the system from **HEMS** (Home Energy Management System)
- Ensure **MQTT** is properly set up and connected to Home Assistant
2. Upload the .yaml files into (`zendure_control.yaml` and optional `energy_monitoring.yaml`) `/homeassistant/packages/`
3. Modify the `/homeassistant/configuration.yaml`:

```yaml
# Configure Packages (Looking in the "packages" folder)
homeassistant:
  packages: !include_dir_named packages

# Exclude certrain entities from spamming the database
recorder:
  exclude:
    entity_globs:
      - automation.zendure_*
      
# Exclude certrain entities from spamming the Activity logbook    
logbook:
  exclude:
    entity_globs:
      - automation.zendure_*
```
4. Upload `secrets.yaml` and replace the placeholders:

| Placeholder | Description |
|-------------|--------------|
| `HUB1_SERIAL_NUMBER` | Serial number of the 1st SolarFlow Hub (e.g., EEA1...) |
| `BAT1_SERIAL_NUMBER` | Serial number of the battery on the 1st Hub (e.g., CO4...) |
| `HUB2_SERIAL_NUMBER` | Serial number of the 2nd SolarFlow Hub — *leave as is if you only have one unit* |
| `BAT2_SERIAL_NUMBER` | Serial number of the battery on the 2nd Hub — *leave as is if you only have one unit* |
| `shellypro3em_SHELLY_ID` | Unique ID of the Shelly 3EM (the part following `shellypro3em_` in the entity ID) or any other power meter entity |
| `notify.YOUR_TELEGRAM_SERVICE` | Name of the notification service (Telegram, mobile app, etc.) |

5. Adapt the person entries:
* person.britta
* person.patrick

6. Restart Home Assistant (No Quick reload)

**Optional:**

7. Block internet access for your Zendure devices via your router or firewall
8. Disable cloud access for the Shelly 3EM Pro

# FAQ

#### How can I tell if everything is installed correctly?
If the installation was successful, you will see a new MQTT device in Home Assistant.  
Under **Entities**, many new entries will appear.  
The easiest way to check is to search for `solarflow` or `zendure` – if results show up, everything is set up correctly.

#### Why is it not working for me?
Have you set `input_boolean.zendure_auto_mode` to `on`?  
The automation will only run when this toggle is enabled.



