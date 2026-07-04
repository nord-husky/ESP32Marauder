<!---[![License: MIT](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/justcallmekoko/ESP32Marauder/blob/master/LICENSE)--->
<!---[![Gitter](https://badges.gitter.im/justcallmekoko/ESP32Marauder.png)](https://gitter.im/justcallmekoko/ESP32Marauder)--->
<!---[![Build Status](https://travis-ci.com/justcallmekoko/ESP32Marauder.svg?branch=master)](https://travis-ci.com/justcallmekoko/ESP32Marauder)--->
<!---Shields/Badges https://shields.io/--->

# ESP32 Marauder
<p align="center"><img alt="Marauder logo" src="https://github.com/justcallmekoko/ESP32Marauder/blob/master/pictures/marauder_skull_patch_04_full_final.png?raw=true" width="300"></p>
<p align="center">
  <b>A suite of WiFi/Bluetooth offensive and defensive tools for the ESP32</b>
  <br><br>
  <a href="https://github.com/justcallmekoko/ESP32Marauder/blob/master/LICENSE"><img alt="License" src="https://img.shields.io/github/license/mashape/apistatus.svg"></a>
  <a href="https://gitter.im/justcallmekoko/ESP32Marauder"><img alt="Gitter" src="https://badges.gitter.im/justcallmekoko/ESP32Marauder.png"/></a>
  <br>
  <a href="https://twitter.com/intent/follow?screen_name=jcmkyoutube"><img src="https://img.shields.io/twitter/follow/jcmkyoutube?style=social&logo=twitter" alt="Twitter"></a>
  <a href="https://www.instagram.com/just.call.me.koko"><img src="https://img.shields.io/badge/Follow%20Me-Instagram-orange" alt="Instagram"/></a>
  <br><br>
</p>
    
[![Build and Push](https://github.com/justcallmekoko/ESP32Marauder/actions/workflows/build_push.yml/badge.svg)](https://github.com/justcallmekoko/ESP32Marauder/actions/workflows/build_push.yml)

## Getting Started
Download the [latest release](https://github.com/justcallmekoko/ESP32Marauder/releases/latest) of the firmware.  

Check out the project [wiki](https://github.com/justcallmekoko/ESP32Marauder/wiki) for a full overview of the ESP32 Marauder

# For Sale Now
You can buy the ESP32 Marauder using [this link](https://www.justcallmekokollc.com)

## Experimental ESP32-2432S022 CYD Target

This tree includes an experimental board target for the 2.2 inch Cheap Yellow Display marked `ESP32-2432S022` / `ESP32-2432S022C`. Use ESP32 Marauder only for authorized WiFi/Bluetooth testing and research.

Status:

- Display: ST7789, 240x320, 8-bit parallel/i80-style bus through LovyanGFX.
- Touch: optional ESP32-2432S022C capacitive I2C touch behind `CYD_2432S022C_TOUCH`.
- SD card: disabled by default because a confirmed ESP32-2432S022 SD pin mapping has not been recovered.
- GPS/buttons: disabled by default because known CYD mappings conflict with this board's display/touch pins.
- Hardware validation: required. A successful compile is not the same as a hardware-tested build.

Build:

```bash
arduino-cli compile \
  --fqbn "esp32:esp32:d32:PartitionScheme=min_spiffs" \
  --warnings none \
  --build-property "compiler.cpp.extra_flags=-DCYD_2432S022" \
  esp32_marauder
```

Build with optional ESP32-2432S022C touch:

```bash
arduino-cli compile \
  --fqbn "esp32:esp32:d32:PartitionScheme=min_spiffs" \
  --warnings none \
  --build-property "compiler.cpp.extra_flags='-DCYD_2432S022 -DCYD_2432S022C_TOUCH'" \
  esp32_marauder
```

Flash, adjusting the serial port for your host:

```bash
arduino-cli upload \
  -p /dev/ttyUSB0 \
  --fqbn "esp32:esp32:d32:PartitionScheme=min_spiffs" \
  esp32_marauder
```

Display pin sources:

- openHASP issue #606: `CS=17`, `DC/RS=16`, `WR=4`, `RD=2`, `D0=15`, `D1=13`, `D2=12`, `D3=14`, `D4=27`, `D5=25`, `D6=33`, `D7=32`, `RST=-1`.
- TFT_eSPI issue #3281 reports the same parallel display mapping and identifies the panel as ST7789 240x320.
- Backlight is unresolved across references. This target defaults to `TFT_BL=0` because TFT_eSPI issue #3281 reports GPIO0 active-high for ESP32-2432S022. Do not reuse GPIO0 as a button unless hardware testing proves the board revision supports it.
- Optional touch comes from openHASP issue #606: `SDA=21`, `SCL=22`, `RST=-1`, address `0x15`, CST820/CST816-family.

If the screen is white or black:

- Verify the build includes `-DCYD_2432S022`.
- Try overriding `SCREEN_ORIENTATION`, `CYD_2432S022_OFFSET_ROTATION`, or `CYD_2432S022_TOUCH_OFFSET_ROTATION` in the build flags.
- Validate the backlight pin on your board revision before changing code. Known candidates from references are GPIO0 and GPIO5; GPIO27 conflicts with the recovered display `D4` mapping and should not be used without stronger evidence.
- If colors are swapped or inverted, test LovyanGFX `rgb_order` / `invert` changes in `esp32_marauder/LGFX_ESP32_2432S022.h` and document the board revision.
