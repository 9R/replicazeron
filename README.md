# Replicazeron FreeCAD

modified parts for QMK powered replicazeron build with STM32F103

![replicazeron](images/replicazeron.JPG "replicazeron")

This repo only contains complementary parts to this project:

[DIY Azeron](https://sites.google.com/view/alvaro-rosati/azeron-keypad-diy-tutorial)

## Features

 * 23 keys
 * analog stick with WASD emulation
 * 5-way dpad
 * OLED display displaying status info and basic configuration
 * 2 status LEDS
 * 6x WS2812 RGB-LED lighting
 * layout editing and oled control from PC using [VIA](https://www.caniusevia.com/) or [Vial](https:/get.vial.today) without the need to reflash the firmware

## BOM

### tldr BOM

In addition to the BOM required for the DYI Azeron & the modified printable [STLs](https://github.com/9R/replicazeron/tree/main/STLs) from this repo you will need the following parts.

The listed parts are only what I used to build the Replicazeron. With minor changes to the configuration a build should be possible with any other MCU & display that is supported by QMK.

|part                        | function                     | quantity    |
|----------------------------|------------------------------|-------------|
| STM32F103 dev board        | MCU                          |           1 |
| ssd1306 128x32             | display                      |           1 |
| WS2812 (on strip)          | addressable LEDs             |           6 |
| M3x10 ISO4762 "cheesehead" | display * LED attachment     |           2 |
| 1N4148 diodes              | switch matrix diodes         |          30 |
| leftover ethernetwire      | wireing & liner              |         <2m |
| bit of bread board         |                              | ~8x13 holes |
| 2.56mm pitch pin headers   |                              |         ~50 |
| dupond jumper wires        |                              |         ~25 |
| some shrink tube           |                              |             |

### MCU
Recommended MCU is an STM32F103 on a bluepill dev board.

A build is also possible with an ATmega32 promicro, but this dev board does not have enough GPIOs to attach a LED strip.

### Display
This build uses a **ssd1306** OLED with 128x32 pixels. The 128x64 pixel version of the ssd1306 should also work but would need a modified housing.

### Adressable LED strip
For optional lighting an addressable WS2612 LED-strip with 6 LEDs is used.

### Wires
I used a bit of old network cable to get some easily distinguishable wires. Bonus: The shielding can be used as a nice looking liner to route the cables from the fingers to the base.

To connect the wireing to the MCU I used 2.56mm pitch dupond wires, pin headers and a bit of bread board.

For the switch matrix to work you will also need 30 signal diodes (i.e. 1N4148).

### Fastener(s)
In addition to the fasteners required for the DIY Azeron you will need one M3x10 screw to attach the display

## Prepare devboard

This step is only required once.

### STM32F103 bluepill

Install the stm32duino bootloader [from this repo](https://github.com/rogerclarkmelbourne/STM32duino-bootloader). The correct prebuild binary for a stm bluepill is `binaries/generic_boot20_pc13.bin`

### STM32F103 bluepill-plus([WeActStudio](https://github.com/WeActStudio/BluePill-Plus))
Install the stm32duino bootloader [from this repo](https://github.com/Mariappan/STM32duino-bootloader/tree/mario). The correct prebuild binary for a stm bluepill is `bootloader_only_binaries/generic_boot20_pb2.bin`

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
   qmk config user.keymap=default
   ```

 - VIA support (optional)
   
   This is necessary to use replicazeron with VIA since the
   removal of via keymaps from QMK in August 2024. 
   ```bash
   # clone userspace keymap repo
   git clone https://github.com/9R/qmk_userspace_via
   
   # add userspace repo to qmk config
   qmk config user.overlay_dir="$(realpath qmk_userspace)"

   # set default keymap to via
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
   # pressing the pinky button second closesed to the palm on the settings layer in default mapping will activate reboot to lash mode
   qmk flash
   ```

## Wireing

Use the [schematics](https://raw.githubusercontent.com/9R/replicazeron_schematics/main/replicazeron_sch.pdf) as reference.

Columns 1 to 4 are the finger towers, the last column attaches the 5way dpad.

Rows 1 to 5 are wired to button 1 to 5 on each finger.

The sixth row is used to connect "special" keys like side keys, analog click and base key.

![electronics](images/replicazeron_electronics.JPG "electronics")

![matrix_connector](images/replicazeron_matrix_connector.JPG "matrix_connector")

### Thumbstick click
To read the thumbstick press via the key matrix you will have to cut some traces on the analog stick pcb and solder the colmn & row wires directly to the button pins.


![analog stick pcb front](images/thumbstick_pcb_cuts.JPG "thumbstick pcb")

## Usage

### Default Keybindings

The default firmware has four input layers. The name of the active layer is displayed on the OLED-screen.

To cycle through layers press the button on the right side of the base.

#### Layer 0: Default
![analog stick pcb front](images/layer_0.jpg "Default layer keybindings")

#### Layer 1 & 2: Shooter / Misc

The second and third layers have no keys assigned by default execpt for the D-Pad and the change-layer-key.
![analog stick pcb front](images/layer_1-2.jpg "Empty layer keybindings")

#### Layer 3: Settings
![analog stick pcb front](images/layer_3.jpg "Settings layer keybindings")

### Tumb Stick Modes

The thumb stick can operate in three modes: `WASD`, `WASD + Shift` and `Joystick`.

In any mode the thumb stick position is available as two axis analog joystick.

The active mode is displayed on the OLED and can be changed on the settings layer (`ThumbMode` key).

| Mode           | Explanation                                                                             |
|----------------|-----------------------------------------------------------------------------------------|
| `WASD`         | `w` `a` `s` `d` key presses based on the position of the thumb stick                    |
| `WASD + Shift` | `w` `a` `s` `d` below and  `W` `A` `S` `D` if thumb stick is above _SHIFTZONE threshold |
| `Joystick`     | `WASD`-Emulation is inactive                                                            |

## Customization

### Keybindings

Default keybindings can be comfortably edited with  [VIA](https://www.caniusevia.com/) or [Vial](https:/get.vial.today) in a webGUI or standalone application. Changes made with the GUI will be saved on the replicazeron, do not require the GUI to permanently run and are persistent over reboots and reflashes. Press `Clr ROM` key to reset to defaults.

Additionally multiple layout presets can be saved and loaded with the GUI on/from a PC.

Even though not recommended, it is also possible to edit default keybindings in the [code](https://github.com/9R/qmk_firmware/blob/replicazeron/keyboards/handwired/replicazeron/keymaps/default/keymap.c#L21-L56).

Available keycodes and their meaning can be found in the [QMK documentation](https://docs.qmk.fm/keycodes).

#### Replicazeron specific keycodes 

|  Key        | Function                                                           |
|-------------|--------------------------------------------------------------------|
| `Reset`     | Reset Controller (to enter firmware flash mode)                    |
| `Clr ROM`   | Clear EPROM. Resets to defaults (keybindings / LED animation)      |
| `ThumbMode` | Cycle through thumbstick modes                                     |
| `AutoRun`   | Toggle autoRun                                                     |

### Thumbstick

The [three parameters](https://github.com/9R/qmk_firmware/blob/replicazeron/keyboards/handwired/replicazeron/config.h#L26-L28), that control the way the thumb stick WASD-emulation behaves currently can only be modified in the source code.

While on the settings layer the OLED display shows debug info that can be used to quickly find alternate values matching personal preference.

| Parameter            | Meaning                                                                                                        |
|----------------------|----------------------------------------------------------------------------------------------------------------|
| _DEADZONE            | Distance from center where no keypresses are send                                                              |
| _SHIFTZONE           | Above this distance from center `W` `A` `S` `D` instead of `w` `a` `s` `d` will be send in `WASD + Shift` mode |
| _THUMBSTICK_ROTATION | Modify to adjust the "forward direction"                                                                       |
 
## Related repos

[replicazeron qmk-firmware fork](https://github.com/9R/qmk_firmware)

[replicazeron via-keyboards fork](https://github.com/9R/keyboards)

[replicazeron vial-qmk firmware fork](https://github.com/9R/vial-qmk)

[replicazeron STM32F103 bluepill schematics](https://github.com/9R/replicazeron_schematics)

## Pictures

![lighting](images/replicazeron_lighting.JPG "lighting")

![oled](images/replicazeron_oled.JPG "oled")
