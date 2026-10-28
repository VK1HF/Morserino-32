# USB Output: vBand and MIDI over the USB cable (M32 Pocket)

*Contributed by Ian Gilchrist, VK1HF. This covers the VK1HF "USB kbd+MIDI" build of the M32 Pocket firmware (environments `pocketwroom-usbio` and `pocketwroom-lora-usbio`); see `VK1HF-ADDITIONS.md` at the top of the repository for how to get it.*

The M32 Pocket can send your keying to a computer **over its USB cable**, in one of two ways:

| Feature (setting value on the Pocket) | What the computer sees | Made for |
|---|---|---|
| **USB:VBAND out** (**VBand**) | A USB keyboard. Left Ctrl is held down while the Morserino keys | **vBand**, the internet CW platform |
| **USB:MIDI out (CTR2-MIDI)** (**MIDI**) | A USB MIDI device: channel 1, note 20 = left paddle, note 21 = right paddle | Software that expects a MIDI CW interface such as the **Lynovation CTR2-MIDI**, for example **SmartSDR for macOS by DL8MRE** with a FlexRadio |
| Off (**Off (charge)**) | Nothing is sent. The USB cable only charges the Pocket and provides the serial port | Charging, or when neither of the above is wanted |

You choose **one** of them at a time, in a single setting.

::: note
This only works on the **M32 Pocket**. Its ESP32-S3 has its own USB controller. The Classic Morserino-32 connects through a USB-to-serial chip and cannot appear as a keyboard or a MIDI device.
:::

## Where to find the setting

1. Start **CW Keyer**.
2. **Double-click the encoder knob** to open the preferences.
3. Turn to **USB Output**, and choose **Off (charge)**, **VBand** (USB:VBAND out) or **MIDI** (USB:MIDI out). The default is **MIDI**.
4. Leave the preferences.

USB output is active only in **CW Keyer**, the same as the Bluetooth keyboard. Everything CW Keyer keys goes out: paddles, a straight key and keyer memories. It does not depend on the **Key Ext Tx** preference, which is only about the transmitter socket. As soon as you leave CW Keyer, any key or note still held down is released.

The serial port stays available on the same cable in every case. The M32 Config Tool, the File Manager and the M32 serial protocol keep working as before.

On the computer, the Pocket appears as **"Morserino-32 Pocket (USB kbd+MIDI)"**.

## USB:VBAND out: vBand over the USB cable

[vBand](https://hamradio.solutions/vband/) is a web site where you can practise and chat in CW with other operators over the internet. Normally it is used with a small USB "dongle" between your paddle and the computer. The Morserino can take the dongle's place.

With **USB Output → VBand**, the Morserino's own keyer forms the dits and dahs. While each one sounds, it holds down the **Left Ctrl** key of a USB keyboard, which vBand reads as a straight key. So in vBand, choose the **straight-key** input. The Morserino has already done the iambic keying.

### USB versus Bluetooth: two different things, never both

The standard firmware can already work with vBand **wirelessly**: **Bluetooth Use → VBand Kbd** makes the Morserino a *Bluetooth* keyboard. That still works in this firmware and has not changed. **USB:VBAND out** (**USB Output → VBand**) is a separate, wired way to do the same job:

| | Bluetooth keyboard (**Bluetooth Use → VBand Kbd**) | USB keyboard (**USB Output → VBand**) |
|---|---|---|
| Connection | Wireless; pair the Morserino with the computer first | The USB cable; nothing to pair |
| Timing | Bluetooth adds delay, and occasionally drops out | Direct, with no radio link in between |
| Battery | Runs on battery | Charges while you use it |

**Use one or the other, not both at the same time.** If both reached the computer, vBand would get every element twice. To make that impossible, the Bluetooth keyboard **does not start while USB Output is set to VBand**, whatever Bluetooth Use says. To go back to Bluetooth, set USB Output to **Off (charge)** or **MIDI**.

## USB:MIDI out (CTR2-MIDI): use the Morserino in place of a CTR2-MIDI

Some radio-control programs accept CW paddles as **MIDI** input. They are built around MIDI CW interfaces such as the **CTR2-MIDI**, designed by **Lyn of Lynovation** ([ctr2.lynovation.com](https://ctr2.lynovation.com)). Credit is due to Lyn: the CTR2-MIDI is an excellent product, and its clear, published MIDI conventions are what made this option easy to get right. If you don't own a Morserino, or want a dedicated interface, the CTR2-MIDI is the one to get.

With **USB Output → MIDI**, the Morserino sends exactly what a CTR2-MIDI sends in its normal paddle mode:

| Paddle | MIDI message |
|---|---|
| Left paddle (dit) | Note **20** on / off, channel **1** |
| Right paddle (dah) | Note **21** on / off, channel **1** |

The **Paddle Polarity** preference is applied first, so "left" always means *your* dit paddle. Because the notes match the CTR2-MIDI's, **software already set up for a CTR2-MIDI works without remapping**. Select the Morserino as the MIDI device in place of the CTR2-MIDI.

### Straight key

Some operators still love their straight keys, and they are covered too. Set **Keyer Mode → Straight Key** (the top line then shows **SK**). Plug the key into the paddle socket; it closes the tip contact, and an ordinary 2-contact plug is fine. The Morserino then sends:

| Key | MIDI message |
|---|---|
| Key down / key up | Note **30** on / off, channel **1** |

Note 30 is the CTR2-MIDI's straight-key note, from its *Extended* paddle mode. In this mode the paddle notes 20 and 21 are **not** sent. In SmartSDR, map **button 30 → CW Straight Key**.

### Keyer memories

A **keyer memory** also goes out over MIDI, as **note 30**. A memory has no paddle movements to send, so the Morserino sends the CW it plays, element by element, as a straight key would. **Map button 30 even if you only use paddles**, otherwise your memories are heard on the Morserino but never reach the radio. Touching a paddle stops a playing memory, so paddle notes and note 30 never overlap, and the radio is never keyed twice.

### Example: SmartSDR for macOS (DL8MRE) with a FlexRadio

[SmartSDR for macOS by Marcus Roskosch, DL8MRE](https://dl8mre.com) can key a FlexRadio from MIDI paddles:

1. Connect the Morserino to the Mac with a USB data cable. Start **CW Keyer** and set **USB Output → MIDI**.
2. In SmartSDR, choose **"Morserino-32 Pocket (USB kbd+MIDI)"** as the MIDI device.
3. Map **button 20 → CW Left Paddle** and **button 21 → CW Right Paddle**, as the CTR2-MIDI manual describes. If you used a CTR2-MIDI before, this mapping is already in place.
4. Also map **button 30 → CW Straight Key**, for keyer memories and a straight key.

You can now use your Morserino as the paddle interface for the radio. Connect your own paddle or key to the Morserino's paddle socket, or use the Pocket's touch paddles.

::: note
**Set the Morserino to the same keying speed (WPM) as SmartSDR.** In this mode SmartSDR's own keyer turns your paddle presses into the CW that is transmitted, while the Morserino's sidetone comes from *its* keyer. If the two speeds differ, what you hear from the Morserino is not what goes out on the air. Use the same keyer mode (for example Iambic B) on both, too. Alternatively, turn the Morserino's sidetone down and listen to the radio's.
:::

To check that notes arrive on a Mac, the free app [MIDI Monitor](https://www.snoize.com/midimonitor/) shows each one. MIDI Monitor names note 20 "G♯-1" and note 21 "A-1".

## Flashing and recovery

This firmware switches the Pocket's USB port from the fixed serial port of the standard firmware to the ESP32-S3's full USB controller. That change is what allows a keyboard and a MIDI device on the same cable. One side effect: a flashing tool can no longer always put the Pocket into download mode by itself. If flashing does not start:

1. Switch the Pocket **off**, and plug in the USB cable.
2. **Hold the FN button** and switch the Pocket **on**. Keep FN held for about two seconds. The screen stays dark; that is the download mode.
3. Flash, then switch the Pocket off and on again.

The official installer at [morserino.info/install.html](https://morserino.info/install.html) can always return the Pocket to the standard firmware the same way.

## How to tell this firmware is running

The boot screen shows the version with **.VK1HF++** added, for example **V9.1.VK1HF++** or **V10.0.VK1HF++**. The M32 Config Tool and the installer still see the plain version number. On the computer, the USB device is called **"Morserino-32 Pocket (USB kbd+MIDI)"**.

## Also in this build: pause before a memory repeats

CW Keyer memories **1 and 2** (shown as R1 and R2) repeat until you touch a paddle. In this build they wait **6 seconds** before each repeat, giving the other station time to answer a CQ.
