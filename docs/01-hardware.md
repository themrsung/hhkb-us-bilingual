# 01 — Hardware: HHKB US (ANSI) layout

> Planning doc. Everything marked `TODO(hardware)` is from memory, PFU's manuals, or the JIS
> sibling repo ([hhkb-ekj-trilingual](https://github.com/themrsung/hhkb-ekj-trilingual)), not
> from a US board on the desk. Check it against the real keyboard and the manual for the exact
> model, then remove the marker.

## The ladder: DIP switches → keymap tool → software

Do as much as possible in the keyboard, and as little as possible in the OS:

1. **DIP switches** (every model). Get the stock row right: `Alt | ◇ | Space | ◇ | Alt`.
2. **Keymap tool** (only models PFU's tool supports). Align the bottom row, and where the OS can
   use it, put **LangToggle in firmware** on the right ◇ (below).
3. **Software** (Karabiner-Elements on macOS, AutoHotkey on Windows, `docs/08-advanced.md` for
   everything else): only for what steps 1–2 couldn't do.

Don't skip a rung. If the user can't or won't do a rung (no keymap tool for the model, no admin
rights, a borrowed board), say so and go to the next one.

## Identify the keyboard

| Model (US) | Connection | Keymap tool | Notes |
| --- | --- | --- | --- |
| **HHKB Professional BT** (PD-KB600B; author's board) | Bluetooth (Classic, not LE). `TODO(hardware)`: as far as known its USB port only supplies **power**, and all typing goes over Bluetooth. Check whether the host sees a USB keyboard at all | **None** `TODO(hardware)`: PFU's HHKB Keymap Tool isn't known to support it | DIP switches → software. Runs on AA batteries or USB power |
| HHKB Professional HYBRID Type-S / HYBRID | USB-C and Bluetooth | HHKB Keymap Tool | One stored keymap. `TODO(hardware)`: check whether the tool keeps separate keymaps per DIP mode |
| HHKB Professional Classic | USB-C | HHKB Keymap Tool | |
| HHKB Studio | USB-C and Bluetooth | HHKB Studio Keymap Tool | **Four keymap profiles** on the board, so each OS can get its own bottom row. Has a pointing stick and gesture pads |
| HHKB Professional 2 and other older boards | USB | None | Same path as the Pro BT: DIP switches → software |

| Property | Value |
| --- | --- |
| Manufacturer | PFU Limited |
| USB vendor ID | `1278` (0x04FE). The Pro BT reports the same vendor ID over Bluetooth (checked on macOS 27, Karabiner-Elements 16.3.0). `TODO(hardware)`: check the HYBRID family over Bluetooth |
| Product ID | Pro BT: **`514`** (over Bluetooth). `TODO(hardware)`: other US models; read it from the device list below. Don't assume it matches a JIS model (JIS HYBRID Type-S is `34`, JIS Studio is `21`) |
| Device name | Pro BT: `HHKB-BT` (no manufacturer string, transport `Bluetooth`). `TODO(hardware)`: other US models. JIS boards show `HHKB-Hybrid`, `HHKB-Studio` (USB) and `HHKB-Studio<n>` (Bluetooth, n = pairing slot) |
| macOS keyboard type | **ANSI** (`40` in `/Library/Preferences/com.apple.keyboardtype`) |

Check on macOS (after Karabiner-Elements is installed):

```sh
'/Library/Application Support/org.pqrs/Karabiner-Elements/bin/karabiner_cli' --list-connected-devices \
  | python3 -c "import json,sys;[print(d.get('product'),d['device_identifiers'],d.get('transport')) for d in json.load(sys.stdin)]"
```

Run it once per connection the user uses (USB and Bluetooth). The Pro BT shows up **once** over
Bluetooth, as a plain keyboard (`is_keyboard` only, no pointing device). The JIS Studio showed up three
times over USB (keyboard, pointing device, consumer) and **once** over Bluetooth, as a combined
keyboard + pointing device (`is_keyboard` and `is_pointing_device` both true). That changes what
Karabiner needs (`03-macos.md`).

On Windows: Device Manager → the keyboard → Details → Hardware Ids (`VID_04FE&PID_….`), or
`Get-PnpDevice -PresentOnly | Where-Object InstanceId -match 'VID_04FE'`. A Bluetooth board may
not show `VID_04FE`; look under Bluetooth devices. On Linux: `lsusb | grep -i 04fe`, or
`bluetoothctl info` for Bluetooth.

## Stock bottom row (US)

Five keys; the two corners of the bottom row are empty.

```
Alt | ◇ | Space | ◇ | Alt
```

◇ sends the HID **GUI** key. macOS calls it Command, Windows calls it Win, Linux calls it
Super/Meta. So the stock row already gives `Aux | System | Space | System | Aux` on every OS. The
only role missing is **LangToggle**, which goes on the right ◇ (see
[`02-layout-spec.md`](02-layout-spec.md)).

`TODO(hardware)`: the Studio's stock US bottom row hasn't been checked. Read it off the Studio
Keymap Tool before changing anything.

## Step 1 — DIP switches (do this FIRST)

The DIP switches are on the underside, under the small cover. Turn the keyboard **off** (power
switch, or take the batteries out on the Pro BT) and unplug it while you flip them.

Recommended setting: keep the stock row. Don't swap Alt and ◇, and don't turn the left ◇ into Fn.
The author's Pro BT, used only with a Mac: **SW1 OFF, SW2 ON, SW3–SW5 OFF, SW6 ON**.

**Pro BT / HYBRID Type-S / HYBRID / Classic US** The Pro BT (PD-KB600B) rows are checked against
the author's board. `TODO(hardware)`: check the other models against their manuals.

| SW | Function | Recommended | Why |
| --- | --- | --- | --- |
| SW1, SW2 | Mode (SW1 OFF + SW2 OFF = HHKB mode, SW1 ON + SW2 OFF = Lite Ext, **SW1 OFF + SW2 ON = Mac mode**) | macOS-only use: **Mac mode** (SW1 OFF, SW2 ON). Otherwise: HHKB mode (both OFF) | Mac mode adds macOS Fn-layer extras. Author's Pro BT: Mac mode. `TODO(hardware)`: check the bottom row's raw codes in Mac mode (Step 3), and what Mac mode does on Windows |
| SW3 | Delete → BackSpace | Your choice | Not part of this layout |
| SW4 | Left ◇ → Fn | **OFF** | Left ◇ must stay System |
| SW5 | Swap Alt and ◇ | **OFF** | ON would give `◇ \| Alt \| Space \| Alt \| ◇`, which puts Aux and System in the wrong places |
| SW6 | Pro BT: **Wake Up** (ON = enabled: a keypress wakes the board from sleep). Other models: `TODO(hardware)` | Pro BT: **ON**. Others: your choice; OFF to start | Author's Pro BT: ON. Not the Studio's inverted power saving. If a Bluetooth board keeps falling asleep, see [`../issues/dip-switch-6-power-saving.md`](../issues/dip-switch-6-power-saving.md) |

**Studio US:** the Studio's switches don't match the HYBRID's. On the JIS Studio the owner's
setting is **all OFF**: SW4 reverses the pointing-stick / gesture-pad **scroll direction**, and SW6
is **inverted power saving** (OFF = power saving on). `TODO(hardware)`: check the US Studio's
manual; the bottom row comes from the keymap tool, not from DIP switches, on the Studio.

> **Set the DIP switches BEFORE the keymap tool.** See
> [`../issues/dip-switch-keymap-reset.md`](../issues/dip-switch-keymap-reset.md). On the JIS
> HYBRID Type-S, changing DIP switches overwrote the stored keymap. On the JIS Studio, flipping
> SW4 (either way) and SW6 (OFF → ON) didn't. `TODO(hardware)`: check on US boards; keep this
> order anyway, and recheck the keymap after any DIP change.

Then check the raw codes (Step 3) before going on.

## Step 2 — Keymap tool (HYBRID family, Classic, Studio)

**Pro BT and Pro 2: skip this step.** There's no keymap tool for them, so LangToggle comes from
software (`03-macos.md`, `04-windows.md`).

Use PFU's tool for the model: **HHKB Keymap Tool** for the HYBRID family and Classic, **HHKB
Studio Keymap Tool** for the Studio. Both are free downloads from the PFU / HHKB site and need the
board connected over **USB**. The keymap is stored on the keyboard, so it can be written from any
computer.

The only job here is the **bottom row**. Leave the rest of the keymap alone.

1. **Align the modifiers:** positions 1, 2 and 5 must send Alt, left ◇ (GUI) and Alt (Step 3
   table). On the HYBRID / Classic, the DIP switches usually already do this.
2. **LangToggle in firmware (right ◇), if the user's OSes allow it:**

   | User's OSes | Set right ◇ to | Then the OS needs |
   | --- | --- | --- |
   | Windows only | **`LANG1`** (the tool may call it "Mac かな" or "Kana") | Nothing: Windows turns `LANG1` into 한/영 (`VK_HANGUL`) natively. Checked on a JIS Studio (`04-windows.md`) |
   | macOS only | **Caps Lock** | Nothing but the "Use the Caps Lock key to switch to and from ABC" setting (`03-macos.md`) |
   | macOS and Windows, one keymap (HYBRID / Classic) | **`LANG1`** | Windows: nothing. macOS: one Karabiner rule, `LANG1` → Caps Lock (`03-macos.md`) |
   | macOS and Windows, **Studio** | one **profile per OS**: macOS profile right ◇ = Caps Lock, Windows profile right ◇ = `LANG1` | Nothing on either OS. Switch profiles with Fn+C, then the profile number (`TODO(hardware)`: check the key combo on the US Studio) |
   | Linux, or anything else | `LANG1` | The IME's toggle set to Hangul (`05-other-os.md`) |

   If the tool can't assign the code the table asks for, leave right ◇ as GUI and do it in
   software. `TODO(hardware)`: check which codes each tool offers. The JIS HYBRID Type-S's tool
   couldn't assign F18, and the JIS Studio's tool could assign F18 and Mac かな.
3. **Studio: set the bottom row on every layer** (Standard, Fn1, Fn2, Fn3), so holding Fn1 or
   Fn2 never changes what those keys send. The JIS repo does this for all its bottom-row keys.
4. Click **Write to HHKB**.

**Studio: importing a settings file (`.hks`) replaces all four profiles.** If the user keeps a
custom keymap on any profile, export it first (the tool's export / save function).

## Step 3 — Check the raw codes

Press each bottom-row key in the OS's key-event viewer, **before** any software remap is in place
(or with the HHKB's modifications turned off for a moment). Do it over **each connection** the
user uses (USB and Bluetooth).

| # | Physical key | Must send (Karabiner name) | Windows / Linux name |
| --- | --- | --- | --- |
| 1 | Left Alt | `left_option` | LAlt / `KEY_LEFTALT` |
| 2 | Left ◇ | `left_command` | LWin / `KEY_LEFTMETA` |
| 3 | Space | `spacebar` | Space / `KEY_SPACE` |
| 4 | Right ◇ | `right_command` (stock) · `japanese_kana` (keymap tool `LANG1`) · `caps_lock` (keymap tool Caps Lock) | RWin / `KEY_RIGHTMETA` (stock) · `VK_HANGUL` 0x15 / `KEY_HANGEUL` (`LANG1`) |
| 5 | Right Alt | `right_option` | RAlt / `KEY_RIGHTALT` (see the Windows note in `04-windows.md`: the Korean layout may turn this into 한/영) |

If they don't match, fix the DIP switches (and then the keymap) before writing any software rule.

## Bluetooth

### HHKB Studio: pairing order

From the JIS repo, checked on a JIS Studio. `TODO(hardware)`: check on a US Studio.

1. Power switch on. Unplug the USB cable.
2. **Fn+Q** → pairing standby (the LEDs sweep side to side).
3. **Fn+Ctrl+n** (n = slot 1–4) → pairing on slot n. This overwrites whatever was paired there.
4. On the host, connect to **HHKB-Studio\<n\>** and type the code it shows, then Enter.
5. **Fn+X** leaves pairing. **Fn+Ctrl+n** on its own switches to the device already on slot n.

**The order matters.** Fn+Ctrl+n *then* Fn+Q paired on the wrong slot, and the Mac's connection
attempt timed out after about 30 s.

**Don't follow the manual's "Fn+C, then 2" step after pairing with a Mac.** That switches the
keymap **profile**, not the Bluetooth slot, and would replace the bottom row the user wrote in
Step 2 with PFU's Profile2.

### HYBRID family and Pro BT

`TODO(hardware)`: pairing keys and slot behavior. The HYBRID family is expected to pair like the
Studio (Fn+Q, then Fn+Ctrl+n). The Pro BT has its own procedure; follow its manual.

On a Bluetooth-only board (the Pro BT), the DIP-first rule still holds, and the batteries come
out while flipping switches. Recheck the raw codes over Bluetooth afterwards.
