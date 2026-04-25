# ZMK Studio serial-port troubleshooting notes

## Symptom

In browser ZMK Studio (`https://zmk.studio/`):

> Failed to open the serial port. Check the permissions of the device and verify it is not in use by another process.

---

## Quick context

- We already found and fixed one PR issue that caused build/link failure:
  - `app/boards/shields/ergo_s1_oe/ergo_s1_oe.dtsi`
  - `zmk,matrix_transform = &physical_layout0;` was wrong.
  - Fixed to: `zmk,physical-layout = &physical_layout0;`
- Current issue is now mostly host/browser USB-serial access + Studio setup.

---

## 1) Verify the keyboard exposes the right serial device

```bash
ls -l /dev/ttyACM* /dev/ttyUSB* 2>/dev/null
readlink -f /dev/serial/by-id/* 2>/dev/null
```

Expected: keyboard appears as `/dev/ttyACM*` (most common).

In the browser picker, use that device entry (not `tty0`, `tty1`, etc.).

---

## 2) Check Linux group permissions (very common)

Check current groups:

```bash
groups
```

On many distros, serial access requires one of:

- `dialout` (Debian/Ubuntu family)
- `uucp` (Arch/Endeavour family)

Add your user if missing:

```bash
sudo gpasswd --add $USER uucp
# or
sudo gpasswd --add $USER dialout
```

Then **log out/in** (or reboot) before retrying.

---

## 3) Check if another process already has the port open

```bash
sudo lsof -nP /dev/ttyACM0
sudo fuser -v /dev/ttyACM0
```

If you see a PID, inspect and stop it:

```bash
ps -fp <PID>
```

Common offenders:

- another browser/tab using Web Serial
- serial monitor (screen/minicom/picocom)
- IDE serial monitor
- ModemManager probing

---

## 4) Test ModemManager interference

```bash
systemctl status ModemManager --no-pager
sudo systemctl stop ModemManager
```

Retry ZMK Studio.

If this fixes it, set a persistent ignore rule later for your device (udev + `ID_MM_DEVICE_IGNORE=1`).

---

## 5) Confirm firmware is Studio-enabled

For Studio over USB, firmware should be built with:

- snippet: `studio-rpc-usb-uart`
- Kconfig: `CONFIG_ZMK_STUDIO=y`

Also useful/required behavior setup:

- keymap includes `&studio_unlock` binding
- for USB Studio use, output endpoint should be USB (`&out OUT_USB`)

---

## 6) Confirm device mode/state

- Make sure board is running normal ZMK firmware, not stuck in bootloader.
- Unplug/replug after flashing.
- For split boards: connect/test from central side (typically left).

---

## 7) Recommended retry sequence

1. Replug keyboard with known data cable.
2. Verify `/dev/ttyACM*` appears.
3. Ensure user is in `uucp`/`dialout` and re-login if changed.
4. Ensure no process is holding the port (`lsof`/`fuser`).
5. Temporarily stop ModemManager and retry.
6. In `https://zmk.studio/`, choose the `/dev/ttyACM*` device.
7. If still failing, rebuild/flash with Studio snippet + config and retry.

---

## Handy commands block

```bash
# devices
ls -l /dev/ttyACM* /dev/ttyUSB* 2>/dev/null
readlink -f /dev/serial/by-id/* 2>/dev/null

# groups
id
groups

# busy port checks
sudo lsof -nP /dev/ttyACM0
sudo fuser -v /dev/ttyACM0

# ModemManager quick test
systemctl status ModemManager --no-pager
sudo systemctl stop ModemManager
```
