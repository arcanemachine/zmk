# ZMK (For Use With the Ergo S-1 Keyboard)

This repo is a fork of [ZMK](https://github.com/zmkfirmware/zmk) that allows you to build custom firmware for your Ergo S-1 keyboard.

> NOTE: This repo is intended to provide customization for the [Ergo S-1](https://github.com/wizarddata/Ergo-S-1) keyboard only. These instructions will probably not work with any other custom keyboards.

To create a custom layout and build your own firmware:

  - First, follow the instructions in [my other GitHub repo](https://github.com/arcanemachine/ergo-s-1-zmk-config) to create a custom keymap file. (The file will be located at `config/ergo_s1_oe.keymap` in the other repo.)
    - You can also just copy the keymap directly from the Keymap Editor web application and paste its contents to the correct path in this repo (described in the next steps).
  - Once you have created a keymap, clone this repo (the one you are currently reading) so you can set up a ZMK development environment and build a custom firmware using your new keymap.
  - Copy your new keymap file to the correct location in this repo (the one you are currently reading): `app/boards/shields/ergo_s1_oe/ergo_s1_oe.keymap`
  - Set up your ZMK development environment (NOTE: The native version did not work for me, but the Dockerized version did): `https://zmk.dev/docs/development/setup`

> NOTE: If you install the Dockerized version, ensure that you are running the following commands from the VS Code terminal that is running inside the container.

  - Once you have installed your ZMK development environment, run the following commands to build the custom firmware for each side of the keyboard:
    - Navigate to the `app` directory: `cd app`
    - Build the firmware for the left side: `west build -d build/left -p -b nice_nano -- -DSHIELD=ergo_s1_oe_left`
    - Build the firmware for the right side: `west build -d build/right -p -b nice_nano -- -DSHIELD=ergo_s1_oe_right`
    - (If using Docker) Move the files to a path that is outside the Docker volume:
      - `mv build/left/zephyr/zmk.uf2 ../ergo_s1_oe_left.uf2`
      - `mv build/right/zephyr/zmk.uf2 ../ergo_s1_oe_right.uf2`
  - Now that you have the firmware files, you can flash them to the keyboard:
    - Double-press the reset button each side of the keyboard to enter the bootloader mode.
    - Plug in a USB cable to the keyboard so you can flash the firmware file
    - The keyboard will appear as a mounted drive in your file explorer.
    - Copy the firmware file for the correct side to this mounted drive.
    - After the file is copied, the keyboard will automatically reset itself, and will be working with the new firmware
    - Now, repeat these steps to flash the correct firmware for the other side of the keyboard.

Congratulations! You have finished flashing your custom keymap to your Ergo S-1 keyboard!

---

Special thanks to the creator of the Ergo S-1 keyboard!
