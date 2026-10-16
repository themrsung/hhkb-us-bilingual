# DIP switch 6: power saving

**Applies to:** HHKB Studio, mostly over Bluetooth (seen on the JIS Studio). Possibly other
Bluetooth boards (HYBRID family, Pro BT), where SW6 also relates to power / wake-up.
`TODO(hardware)`: check what SW6 does on each US model.
**Status:** workaround; the JIS repo's author recommends it if you hit the symptom.

## Symptom

Over Bluetooth, the keyboard keeps going to sleep. You have to wake it again, and the first
keypress may be lost.

## Cause (JIS Studio)

The Studio's power saving is **very aggressive**, and **SW6 is inverted**:

| SW6 | Power saving |
| --- | --- |
| **OFF** (the default, "all OFF") | **On** |
| **ON** | **Off** |

## What to do

- **Studio:** if the keyboard keeps sleeping over Bluetooth, **flip SW6 ON** to turn power saving
  off. Expect shorter battery life. JIS repo, tested 2026-10-17: flipping SW6 OFF → ON kept the
  custom keymap, the active profile and the Bluetooth pairing slot. No re-pairing and no keymap
  rewrite were needed.
- **Pro BT / HYBRID family:** check the manual for SW6 before flipping it. On a board that runs on
  batteries (the Pro BT), turning power saving off costs battery life; tell the user.
- Change SW6 only if you have the symptom. The default stays OFF.
