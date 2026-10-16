# DIP switches can reset the keymap

**Applies to:** boards with PFU's keymap tool (`../docs/01-hardware.md`, Step 2). Not the Pro BT
or Pro 2, which have no stored keymap to lose.
**Status:** seen on the JIS HYBRID Type-S; not seen on the JIS Studio. `TODO(hardware)`: check on
US boards.

## Symptom

After flipping a DIP switch, the bottom row is back to PFU's stock codes. The keymap written with
the keymap tool is gone.

## What was seen (JIS repo)

| Board | DIP change | Keymap kept? |
| --- | --- | --- |
| JIS HYBRID Type-S | any change | **No**: the stored keymap was overwritten and had to be written again |
| JIS Studio | SW4 ON → OFF, OFF → ON → OFF | Yes (also kept the active profile) |
| JIS Studio | SW6 OFF → ON (over Bluetooth, on Profile 4) | Yes (also kept the active profile and the Bluetooth pairing slot) |

The Studio's other switches weren't tested.

## What to do

- **Always set the DIP switches first, then write the keymap.** That's the order in
  `../AGENTS.md` and `../docs/01-hardware.md`.
- After any later DIP change, check the bottom row again (`../docs/01-hardware.md`, Step 3), and
  write the keymap again if it's gone.
- Studio: export the settings from the keymap tool before experimenting, so a reset costs one
  import.
