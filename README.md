# Replicazeron FreeCAD

modified parts for QMK powered replicazeron build with STM32F103

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
   make keyboards/handwired/replicazeron/stm32:via
   ```
 - Flash firmware with ```dfu-util```
   ```bash
   dfu-util -a 2 -d 1EAF:0003  -D .build/handwired_replicazeron_stm32_via.bin
   ```
 - Or  build & Flash firmware in one go
   ```bash
   make keyboards/handwired/replicazeron/stm32:via:dfu-util # this only works after qmk is running on the stm32
   ```
 


## Related repos

[replicazeron qmk-firmware fork](https://github.com/9R/qmk_firmware)

[replicazeron STM32F103 bluepill schematics](https://github.com/9R/replicazeron_schematics)
