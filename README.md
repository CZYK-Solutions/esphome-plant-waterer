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
    ref: main
    refresh: 1s

substitutions:
  device_name: plant-waterer
  friendly_name: Plant Waterer
  dry_pct_default: 30
  wet_pct_default: 60
  dry_adc_default: "1.795"
  wet_adc_default: "1.390"
```

## What the package includes

The package defined in `base.yaml` sets up:

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
- **Blinking alert**:
    - LED blinks in current color if soil is too dry and the status light is off
- **Physical button actions**:
    - Single click: show current status (LED on for 1s)
    - Double click: water once (pump runs for set duration)
    - Long press: toggle status LED
- **Home Assistant integration**:
    - All controls and sensors are available as entities
- **Customizable thresholds** for dry/wet levels and ADC calibration

### Calibration mode (for dry/wet calibration)

Calibration is controlled by a dedicated switch in Home Assistant called `Calibration Mode`.

**To calibrate the soil moisture sensor:**

1. **Dry calibration:**
  - Disconnect the sensor from any moisture (let it air dry or keep it in dry soil).
  - Enable the `Calibration Mode` switch.
  - The value of `Soil Dry Voltage (V)` will automatically update if the measured value is higher than the current setting.  
    (If the value is lower, you can manually override it.)

2. **Wet calibration:**
  - Place the sensor in a glass of water (fully wet).
  - The value of `Soil Wet Voltage (V)` will automatically update if the measured value is lower than the current setting.  
    (If the value is higher, you can manually override it.)

3. **Wait:**
  - For both dry and wet calibration, it is recommended to leave the sensor in each state for about 15 minutes to allow readings to stabilize.

4. **Finish:**
  - Disable the `Calibration Mode` switch when done.

You can always manually adjust `soil_dry_adc` and `soil_wet_adc` in Home Assistant if needed.

## Deploying updates

After the initial USB flash, subsequent changes can be uploaded over-the-air from the ESPHome dashboard.

ESPHome will pull the latest `esphome.yaml` from this repository on every build (the `refresh` option forces a check every second when building), so keeping your local file minimal ensures you always use the newest features and settings.
