# Replicazeron FreeCAD

modified parts for QMK powered replicazeron build with STM32F103

![replicazeron](images/replicazeron.JPG "replicazeron")

This repo only contains complementary parts to this project:

https://sites.google.com/view/alvaro-rosati/azeron-keypad-diy-tutorial

## Features

 * 23 keys
 * analog stick with WASD emulation
 * 5-way dpad
 * OLED display displaying status info and basic configuration
 * 2 status LEDS
 * 6x WS2812 RGB-LED lighting
 * layout editing and oled control using [via](https://www.caniusevia.com/) withou the need to reflash

## Prepare devboard

This step is only required once.

### STM32F103 bluepill

Install the stm32duino bootloader [from this repo](https://github.com/rogerclarkmelbourne/STM32duino-bootloader). The correct prebuild binary for a stm bluepill is `binaries/generic_boot20_pc13.bin`

Flash the bootloader with [stlink](https://github.com/stlink-org/stlink).

### ProMicro (atmega32)

Install caterina bootloader on you promicro

## HowTo: Build & flash QMK

 - clone firmware
   ```bash
   git clone https://github.com/9R/qmk_firmware
   ```

 - setup qmk environment via package management or in python venv
   ```bash
   python -m venv venv
   source venv/bin/activate # you'll need to repeat this after leaving the venv. the other steps only need to be executed once
   pip install qmk
   ```

 - Set qmk defaults
   ```bash
   #set default keyboard:
   qmk config user.keyboard=handwired/replicazeron

   #if you are using a promicro specify the variant:
   qmk config user.keyboard=handwired/replicazeron/promicro

   #set default keymap:
   qmk config user.keymap=via
   ```

 - Build firmware
   ```bash
   cd qmk-firmware

   qmk compile
   ```

 - First time flash firmware with ```dfu-util```
   ```bash
   dfu-util -a 2 -d 1EAF:0003 -D .build/handwired_replicazeron_stm32_via.bin
   ```

 - After qmk is running on the controller, build and flash in one go with
   ```bash
   qmk flash
   ```
 
## Related repos

[replicazeron qmk-firmware fork](https://github.com/9R/qmk_firmware)

[replicazeron via-keyboards fork](https://github.com/9R/keyboards)

[replicazeron STM32F103 bluepill schematics](https://github.com/9R/replicazeron_schematics)

## Pictures


![lighting](images/replicazeron_lighting.JPG "lighting")

![oled](images/replicazeron_oled.JPG "oled")

## Wireing

Use the [schematics](https://raw.githubusercontent.com/9R/replicazeron_schematics/main/replicazeron_sch.pdf) as reference.

The buttons are wired as a matrix with 6 rows and 5 columns. The diode direction is column to row. Columns 1-4 are the finger towers, the last column attaches the 5-way dpad. Rows 1 to 5 are wired to buttons 1 to 5 on each finger, the sixth row is used to connect "special" keys like side keys, analog stick click and base key. See keymap code in the qmk repo at `keyboards/handwired/replicazeron/keymaps/*/keymap.c` for details.

![electronics](images/replicazeron_electronics.JPG "electronics")

![matrix_connector](images/replicazeron_matrix_connector.JPG "matrix_connector")

### Thumbstick click
To read the thumbstick press via the key matrix you will have to cut some traces on the analog stick pcb and solder the colmn & row wires directly to the button pins.


![analog stick pcb front](images/thumbstick_pcb_cuts.JPG "thumbstick pcb")
