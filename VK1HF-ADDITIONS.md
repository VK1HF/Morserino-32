# VK1HF additions to the Morserino-32 firmware

This is **Ian Gilchrist VK1HF's copy** of the official [Morserino-32 repository](https://github.com/oe1wkl/Morserino-32) by Willi Kraml, OE1WKL. It adds a few features to the **M32 Pocket** that are not (yet) in Willi's official firmware, so you can try them before any official release.

> **Unofficial.** These additions are not made or supported by Willi. Please report problems with them **here**, on [VK1HF/Morserino-32](https://github.com/VK1HF/Morserino-32/issues), not to the official project. If anything goes wrong, the [official installer](https://morserino.info/install.html) puts the standard firmware back.

## What's added

| Feature | What it does |
|---|---|
| **USB:VBAND out** | Use vBand over the **USB cable** instead of Bluetooth. Never both at once |
| **USB:MIDI out (CTR2-MIDI)** | The Pocket acts like a **Lynovation CTR2-MIDI**: paddles as MIDI notes 20/21, a straight key as note 30, and keyer memories sent the same way a person would send them. For example, key a FlexRadio from **SmartSDR for macOS (DL8MRE)** |
| **Memory repeat pause** | CW Keyer memories R1 and R2 wait 6 seconds before repeating, giving time for a reply to your CQ |
| **Version mark** | The boot screen shows e.g. **V10.0.VK1HF++**, so you can tell this firmware is running |

How to use them: **[USB Output guide](Documentation/USB%20Output/USB-Output-Guide.md)**.

**M32 Pocket only.** The Classic Morserino-32 cannot appear as a USB keyboard or MIDI device. Its builds still compile from this repository, but without the USB features.

## Which branch

| Branch | Based on | For |
|---|---|---|
| **`v9.1-usb-kbd-midi`** (**recommended**, the default) | The official **V9.1** release | V9.1 plus these additions. This is what VK1HF runs and has tested |
| **`usb-kbd-midi`** (*experimental*) | Willi's development code (V10, **beta**) | Willi's work in progress plus these additions. V10 is a beta with known rough edges, and this combination is less tested |

## The easy way: let Claude Code do it

[Claude Code](https://claude.com/claude-code) can download, build and flash this for you; that is how these additions were made. Connect the Pocket by USB, open Claude Code in an empty folder, and tell it something like:

> Clone https://github.com/VK1HF/Morserino-32, branch `v9.1-usb-kbd-midi`. Read `VK1HF-ADDITIONS.md`, then build the `pocketwroom-usbio` firmware with PlatformIO and flash it to my M32 Pocket. Walk me through the FN-button step.

Use `pocketwroom-lora-usbio` instead if your Pocket has LoRa: it has an antenna socket, and **LoRa Trx** under Transceiver in the menu.

## The manual way: build and flash it yourself

You need **git**, **Python 3** and **[PlatformIO](https://platformio.org/install/cli)**.

```sh
git clone -b v9.1-usb-kbd-midi https://github.com/VK1HF/Morserino-32.git     # or -b usb-kbd-midi for V10 (beta)
cd Morserino-32/Software/src
pio run -e pocketwroom-usbio          # or pocketwroom-lora-usbio for a LoRa Pocket
```

The files to flash end up in `.pio/build/pocketwroom-usbio/`.

**Put the Pocket into download mode.** Once this firmware is installed, a flashing tool can no longer switch the Pocket into download mode by itself, so always do it by hand:

1. Switch the Pocket **off**, and connect it by USB.
2. **Hold FN** and switch the Pocket **on**. Keep FN held for about two seconds. The screen stays dark.

**Flash it.** Use your serial port, for example `/dev/ttyACM0` on Linux or `/dev/cu.usbmodem…` on a Mac:

```sh
B=.pio/build/pocketwroom-usbio
python3 ~/.platformio/packages/tool-esptoolpy/esptool.py --chip esp32s3 -p /dev/ttyACM0 -b 921600 \
  --before no_reset --after no_reset write_flash -z \
  0x0 $B/bootloader.bin 0x8000 $B/partitions.bin \
  0xe000 ~/.platformio/packages/framework-arduinoespressif32/tools/partitions/boot_app0.bin \
  0x10000 $B/firmware.bin
```

Then switch the Pocket off and on. Your settings are kept.

## Credits

- **Willi Kraml, OE1WKL**, for the Morserino-32 and its firmware. Everything here builds on his work.
- **Lyn of Lynovation**, for the excellent [CTR2-MIDI](https://ctr2.lynovation.com) and its clearly documented MIDI conventions, which the MIDI output follows exactly.
- **Marcus Roskosch, DL8MRE**, for SmartSDR for macOS.
- Built by **Ian Gilchrist, VK1HF**, with Claude Code.
