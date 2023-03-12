# Replicazeron FreeCAD

modified parts for QMK powered replicazeron build with STM32F103

![replicazeron](images/replicazeron.JPG "replicazeron")

This repo only contains complementary part to this project:

https://sites.google.com/view/alvaro-rosati/azeron-keypad-diy-tutorial

## HowTo: Build & flash QMK

 - clone firmware
   ```bash
   git clone https://github.com/9R/qmk_firmware
   ```
 - setup qmk environment via package management or in python venv
   ```bash
   python -m venv venv
   cd venv
   pip install qmk
   ```
 - Build firmware
   ```bash
   cd qmk-firmware
   make handwired/replicazeron/stm32f103:via
   ```
 - Flash firmware with ```dfu-util```
   ```bash
   dfu-util -a 2 -d 1EAF:0003  -D .build/handwired_replicazeron_stm32_via.bin
   ```
 - Or  build & Flash firmware in one go
   ```bash
   make handwired/replicazeron/stm32:via:dfu-util # this only works after qmk is running on the stm32
   ```
 
## Related repos

[replicazeron qmk-firmware fork](https://github.com/9R/qmk_firmware)

[replicazeron via-keyboards fork](https://github.com/9R/keyboards)

[replicazeron STM32F103 bluepill schematics](https://github.com/9R/replicazeron_schematics)

## Pictures


![lighting](images/replicazeron_lighting.JPG "lighting")

![oled](images/replicazeron_oled.JPG "oled")

## Wireing

The buttons are wired as a matrix with 6 rows and 5 columns. The diode direction is column to rowi. Columns 1-4 are the finger towers, the last column attaches the 5way dpad. rows 1-5 are wired to button 1-5 on each finger, the sixth row is used to connect "special" keys like side keys, analog click and base key. See keymap code in the qmk repo for details. To read the thumbstick press via the key matrix you will have to cut some traces on the analog stick pcb and solder the colmn & row wires directly to the button pins.

![electronics](images/replicazeron_electronics.JPG "electronics")

![matrix_connector](images/replicazeron_matrix_connector.JPG "matrix_connector")
