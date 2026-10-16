# 01 — Hardware: HHKB US (ANSI) layout

> Planning doc. Everything marked `TODO(hardware)` is from memory or PFU's manuals, not from a
> board on the desk. Check it against the real keyboard and the manual for the exact model, then
> remove the marker.

## Identify the keyboard

| Property | Value |
| --- | --- |
| Models in scope | HHKB Professional HYBRID Type-S US, HYBRID US, Classic US, HHKB Studio US. `TODO(hardware)`: which one the author owns |
| Manufacturer | PFU Limited |
| USB vendor ID | `1278` (0x04FE) |
| USB product ID | `TODO(hardware)`: read from the device list below. Don't assume it matches the JIS model (JIS HYBRID Type-S is `34`, JIS Studio is `21`) |
| Device name (Karabiner) | `TODO(hardware)` (JIS boards show `HHKB-Hybrid` / `HHKB-Studio`) |
| macOS keyboard type | **ANSI** |

Check on macOS (after Karabiner-Elements is installed):

```sh
'/Library/Application Support/org.pqrs/Karabiner-Elements/bin/karabiner_cli' --list-connected-devices \
  | python3 -c "import json,sys;[print(d.get('product'),d['device_identifiers']) for d in json.load(sys.stdin)]"
```

On Windows: Device Manager → the keyboard → Details → Hardware Ids (`VID_04FE&PID_….`). On Linux:
`lsusb | grep -i 04fe`.

## Stock bottom row (US)

Five keys; the two corners of the bottom row are empty.

```
Alt | ◇ | Space | ◇ | Alt
```

◇ sends the HID **GUI** key. macOS calls it Command, Windows calls it Win, Linux calls it
Super/Meta. So the stock row already gives `Aux | System | Space | System | Aux` on every OS. The
only role missing is **LangToggle**, which goes on the right ◇ (see
[`02-layout-spec.md`](02-layout-spec.md)).

## Step 1 — DIP switches (do this FIRST)

The DIP switches are on the underside, under the small cover. The keyboard should be **off or
unplugged** while you flip them.

Recommended setting: keep the stock row. Don't swap Alt and ◇, and don't turn the left ◇ into Fn.

**HYBRID Type-S / HYBRID / Classic US** `TODO(hardware)`: check every row against the manual
for the exact model.

| SW | Function | Recommended | Why |
| --- | --- | --- | --- |
| SW1, SW2 | Mode (both OFF = HHKB mode, SW1 ON = Mac mode, SW2 ON = Lite Ext) | macOS-only use: **Mac mode** (SW1 ON, SW2 OFF). Otherwise: HHKB mode (both OFF) | Mac mode adds macOS Fn-layer extras. The bottom row's HID codes are the same either way. `TODO(hardware)`: confirm, and check what Mac mode does on Windows |
| SW3 | Delete → BackSpace | Your choice | Not part of this layout |
| SW4 | Left ◇ → Fn | **OFF** | Left ◇ must stay System |
| SW5 | Swap Alt and ◇ | **OFF** | ON would give `◇ \| Alt \| Space \| Alt \| ◇`, which puts Aux and System in the wrong places |
| SW6 | Power / wake-up behavior | Your choice | `TODO(hardware)`: name the exact function |

**Studio US:** `TODO(hardware)`. The Studio's DIP switches don't match the HYBRID's (on the JIS
Studio, SW4 reverses scroll). Check its manual, keep the bottom row stock, then confirm in Step 3.

> **Set the DIP switches BEFORE the keymap tool.** On the JIS HYBRID Type-S, changing DIP
> switches overwrote the stored keymap. `TODO(hardware)`: check on US boards; keep this order
> anyway.

## Step 2 — Keymap tool (usually not needed)

With the recommended DIP settings, the board already sends what this layout needs, and the
LangToggle remap happens in software (per OS). Use PFU's keymap tool (HHKB Keymap Tool for the
HYBRID family, the Studio's own tool for the Studio) only if:

- the user wants a different bottom row (`06-customization.md`), or
- the user uses only one OS and wants LangToggle in firmware (for example right ◇ → Caps Lock for
  macOS-only use). This saves the software remap, but the firmware keymap is the same on every
  machine the board connects to, so it only makes sense with a single OS.
  `TODO(hardware)`: check which key codes the keymap tool can assign (Caps Lock? Hangul / Lang1?).

## Step 3 — Check the raw codes

Press each bottom-row key in the OS's key-event viewer, **before** any software remap is in place
(or with the HHKB's modifications turned off for a moment):

| # | Physical key | Must send (Karabiner name) | Windows / Linux name |
| --- | --- | --- | --- |
| 1 | Left Alt | `left_option` | LAlt / `KEY_LEFTALT` |
| 2 | Left ◇ | `left_command` | LWin / `KEY_LEFTMETA` |
| 3 | Space | `spacebar` | Space / `KEY_SPACE` |
| 4 | Right ◇ | `right_command` | RWin / `KEY_RIGHTMETA` |
| 5 | Right Alt | `right_option` | RAlt / `KEY_RIGHTALT` |

If they don't match, fix the DIP switches before writing any software rule.
