# Plant Waterer – ESPHome Package

This repository contains an ESPHome package for automating plant watering using an ESP32 (tested with the M5Stack Atom). It measures soil moisture, controls a water pump, and provides visual feedback via an RGB LED.

## Hardware overview

- **Board**: ESP32 (e.g., [M5Stack Atom](https://m5stack.com/products/atom-lite-esp32-development-kit))
  - **Physical button**: connected to `GPIO39` (for manual actions)
  - **Status RGB LED**: WS2812, connected to `GPIO27`
- **Watering Unit**: [M5Stack Watering Unit](https://www.tinytronics.nl/nl/platformen-en-systemen/m5stack/unit/m5stack-watering-unit-bodemvochtsensor-en-waterpomp)
  - **Soil moisture sensor**: analog output connected to `GPIO32`
  - **Water pump relay**: controlled via `GPIO26`

## Getting started

1. Install ESPHome (Home Assistant add-on or ESPHome CLI).
2. Create or select a new ESPHome device (e.g., `plant-waterer.yaml`).
3. Flash the device once over USB to enable OTA updates.

## Add the package to your ESPHome config

Your device config can simply include the package from this repository.  
See the example below for the required `packages` and `substitutions` keys.

```yaml
packages:
  base:
    url: https://github.com/CZYK-Solutions/esphome-plant-waterer
    files: [esphome.yaml]

substitutions:
  device_name: plant-waterer
  friendly_name: Plant Waterer
  dry_pct_default: 30
  wet_pct_default: 60
```

## What the package includes

The package defined in `esphome.yaml` sets up:

- ESP32 board configuration (Arduino framework)
- Soil moisture sensor (ADC on `GPIO32`)
- Water pump relay control (`GPIO26`)
- RGB status LED (WS2812 on `GPIO27`)
- Physical button on `GPIO39` with multi-click support
- Template buttons and numbers for manual watering, calibration, and thresholds
- Automatic calibration of dry/wet ADC values
- Visual feedback and warnings via the RGB LED

## Features

- **Soil moisture measurement**
- **Calibration mode for setting dry/wet points**
- **Water pump control** with adjustable duration
- **RGB LED color gradient**:
    - Amber (dry) → Green (optimal) → Blue (wet)
    - Smooth color transition based on soil moisture percentage
- **Physical button actions**:
    - Single click: show current status (LED on for 1s)
    - Double click: water once (pump runs for set duration)
    - Long press: toggle status LED
- **Home Assistant integration**:
    - All controls and sensors are available as entities
- **Customizable thresholds** for dry/wet levels and ADC calibration

### Calibration mode (for dry/wet calibration)

You can calibrate the sensor using two buttons in Home Assistant:

**To calibrate the soil moisture sensor:**

1. Disconnect the sensor from any moisture (let it air dry or keep it in dry soil).
2. Press the `Calibrate Dry` button.
   - The calibration will run and show progress in the `Soil Calibration State` and `Soil Calibration Instruction` text sensors.
   - Wait until dry calibration completes.
3. Place the sensor in a glass of water (fully wet). 
   - Wait until `Soil Moisture Calibrated (V)` the sensor stabilizes (about 5 minutes).
4. Press the `Calibrate Wet` button.
   - The calibration will run and show progress in the text sensors.
   - Wait until calibration completes.
5. **Manual adjustment:**
   - You can always manually adjust `Soil Dry Voltage (V)` and `Soil Wet Voltage (V)` in Home Assistant if needed.

**Calibration phases:**
- `Uncalibrated` (phase 0): Neither dry nor wet is calibrated.
- `Calibrating Dry` (phase 1): In progress.
- `Calibrating Wet` (phase 2): In progress.
- `Calibrated` (phase 3): Both dry and wet are calibrated.

**Note:**  
Calibration is only complete when both dry and wet calibration phases are finished.  
During calibration, the text sensors will show the current progress and instructions.  
The sensor will show as "Uncalibrated" only when not calibrating and not yet calibrated.

## Deploying updates

After the initial USB flash, subsequent changes can be uploaded over-the-air from the ESPHome dashboard.

ESPHome will pull the latest `esphome.yaml` from this repository on every build, so keeping your local file minimal ensures you always use the newest features and settings.
