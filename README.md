# ZMK (For Use With the Ergo S-1 Keyboard)

## Intro

This repo is a fork of [ZMK](https://github.com/zmkfirmware/zmk) that allows you to build custom firmware for your Ergo S-1 keyboard.

> NOTE: This repo is intended to provide customization for the [Ergo S-1](https://github.com/wizarddata/Ergo-S-1) keyboard only. These instructions will probably not work with any other custom keyboards.

## Quick Start

To create a custom layout and build your own firmware:

  - First, follow the instructions in [my other GitHub repo](https://github.com/arcanemachine/ergo-s-1-zmk-config) to create a custom keymap file. (The file will be located at `config/ergo_s1_oe.keymap` in the other repo.)
    - You can also just copy the keymap directly from the Keymap Editor web application and paste its contents to the correct path in this repo (described in the next steps).
  - Once you have created a keymap, clone this repo (the one you are currently reading) so you can set up a ZMK development environment and build a custom firmware using your new keymap.
  - Copy your new keymap file to the correct location in this repo (the one you are currently reading): `app/boards/shields/ergo_s1_oe/ergo_s1_oe.keymap`
  - Set up your ZMK development environment (NOTE: The native version did not work for me, but the Dockerized version (using Devcontainers CLI) did work): [Link to docs](https://zmk.dev/docs/development/local-toolchain/setup/container?container=cli), [Archived link to docs](https://archive.is/hkw83)

> NOTE: If you install the Dockerized version, ensure that you are running the following commands from the Devcontainers CLI terminal that is running inside the container.

  - Once you have installed your ZMK development environment, run the following commands to build the custom firmware for each side of the keyboard:
    - Navigate to the `app` directory: `cd app`
    - Build the firmware for the left side: `west build -d build/left -p -b nice_nano -- -DSHIELD=ergo_s1_oe_left -DZMK_CONFIG=/workspaces/zmk/app/boards/shields/ergo_s1_oe`
    - Build the firmware for the right side: `west build -d build/right -p -b nice_nano -- -DSHIELD=ergo_s1_oe_right -DZMK_CONFIG=/workspaces/zmk/app/boards/shields/ergo_s1_oe`
    - (If using Docker) Move the files to a path that is outside the Docker volume:
      - `mv build/left/zephyr/zmk.uf2 ../ergo_s1_oe_left.uf2`
      - `mv build/right/zephyr/zmk.uf2 ../ergo_s1_oe_right.uf2`
  - Now that you have the firmware files, you can flash them to the keyboard. Perform the following instructions for each side of the keyboard:
    - Double-press the reset button on the back of the keyboard to enter the bootloader mode.
    - Plug in a USB cable to the keyboard so you can flash the firmware file.
    - The keyboard will appear as a mounted drive in your file explorer.
    - Copy the firmware file for the correct side to this mounted drive.
    - After the file is copied, the keyboard will automatically reset itself, and will be working with the new firmware.
    - Now, repeat these steps to flash the correct firmware for the other side of the keyboard.

Congratulations! You have finished flashing your custom keymap to your Ergo S-1 keyboard!

> TIP: You can add custom config rules by editing `app/boards/shields/ergo_s1_oe/ergo_s1_oe.conf` before building the firmware.
>
> The `-DZMK_CONFIG=/workspaces/zmk/app/boards/shields/ergo_s1_oe` flag ensures that the config is loaded from `app/boards/shields/ergo_s1_oe/`
>
> (NOTE: This is not the most idiomatic way to do this. You *should* put custom config in your `zmk-config` repo, then create the `zmk-config` Docker volume with the correct path... but this is easier to work with if you just need to tweak a config value or two.)

## How this repo works

This repo is just a few custom commits cherry-picked on top of the ZMK main branch.

I may update this repo from time to time. If not, it is easy to do it yourself by cherry-picking the newest commits (by me) on top of the ZMK main branch. It shouldn't cause any merge conflicts.

> NOTE: Due to how I keep this repo up-to-date, the `main` branch is likely to break if you apply any changes on top of my commits, then try to pull again. In that case, just pull my main branch, make a new branch, then cherry-pick your commits on top of my commits. I'm going for personal convenience here, in terms of keeping this thing up to date. Sorry if I cause any merge conflicts for you because of my laziness. :)

## Attribution

Special thanks to the creators of the Ergo S-1 keyboard, and the creators of ZMK!
