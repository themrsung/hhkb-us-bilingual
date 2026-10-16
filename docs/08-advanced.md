# 08 — Advanced: your own setup, or custom firmware

For the **Advanced** prompt in the README. Two paths:

- **A. Bring your own setup.** The user already has a remapper or firmware they like (kanata,
  keyd, Hammerspoon, QMK/VIA, an existing Karabiner or AutoHotkey config), or an OS the other
  prompts don't cover (`05-other-os.md`). The agent fits the layout into it.
- **B. Custom firmware.** The user wants the layout in the keyboard itself, beyond what PFU's
  keymap tool can do, on a replacement controller.

Nothing here is tested. No binaries or scripts go into this repo, on either path.

## Start by asking

Ask in one batch, and show the default for each:

1. **Which path**, A or B. Default: A.
2. **Board and connections** (`01-hardware.md`): model, USB / Bluetooth, DIP switch settings now.
3. **OSes** the board is used with, and which one is primary.
4. **What's already configured**: remapper and its config file, firmware and keymap source,
   IMEs. Ask the user to point at the files or paste them. Read them before proposing anything.
5. **The two languages** and **the bottom row** (defaults: English ⇄ Korean,
   `Aux | System | Space | LangToggle | Aux`).

## Path A: fit the layout into the user's setup

1. Walk the ladder anyway (`01-hardware.md`): DIP switches, then the keymap tool if the model has
   one. Whatever the hardware does, the user's tool doesn't have to.
2. Translate [`02-layout-spec.md`](02-layout-spec.md) into the user's tool. Keep its style
   (layers, naming, file layout). Starting points:

   | Tool | Right ◇ → LangToggle |
   | --- | --- |
   | kanata | Put `rmet` in `defsrc` and the OS's LangToggle in the base layer: `caps` (macOS Caps Lock switching), `C-spc`, or the Hangul / `LANG1` key (look up its name in kanata's key list) on Windows and Linux |
   | keyd | `rightmeta = hangeul` under `[ids] 04fe:*` (`05-other-os.md`) |
   | Hammerspoon (macOS) | Post a key event (Caps Lock or Ctrl+Space), **don't** select the source directly (`../issues/macos-cjk-select-desync.md`) |
   | QMK / VIA / Vial | `KC_LNG1` (Windows / Linux) or `KC_CAPS` (macOS), on every layer |
   | Existing Karabiner / AutoHotkey config | Merge the rule from `03-macos.md` / `04-windows.md`, don't add a second file that fights the first |

3. Show the user the diff, back up the file, apply, and run `07-verification.md`.

Keep the repo's rules: scope to the HHKB where the tool can, merge instead of overwriting, ask
before installing anything.

## Path B: custom firmware

PFU's stock firmware on the Pro BT, HYBRID family and Studio isn't known to be replaceable with
open firmware. `TODO(hardware)`: check for the user's exact model at the time of setup. So
"custom firmware" here usually means **a replacement controller board** that runs open firmware
(TMK, QMK or ZMK), fitted inside the HHKB case.

Before anything else, tell the user plainly:

- It means **opening the case** and swapping the controller. That can void the warranty, and the
  stock controller's features (PFU's Bluetooth, battery handling, the Studio's pointing stick and
  gesture pads) may not carry over.
- The agent can't do the hardware part. It can research, write the keymap, and build it.
- Ask whether they want to go ahead before researching boards.

Then:

1. **Research what fits the exact model** (Pro BT, HYBRID, Studio, Pro 2 differ in case, PCB
   connector and power). Give the user the options with sources and dates, and let them choose.
   Don't pick for them, and don't buy anything.
2. **Write the keymap** for the chosen firmware from `02-layout-spec.md`. Map LangToggle to a code
   every OS the user has understands:

   | OS | Firmware code for LangToggle | Notes |
   | --- | --- | --- |
   | Windows | `LANG1` (QMK `KC_LNG1`) | Native 한/영 (JIS repo: checked from a Studio's Mac かな key) |
   | Linux | `LANG1` | `KEY_HANGEUL`; set the IME's toggle to Hangul |
   | macOS | `KC_CAPS` with Caps Lock switching on, or `LANG1` + the Karabiner LANG1 rule (`03-macos.md`) | macOS has no native `LANG1` handling for Korean |

   For a board shared between macOS and other OSes, offer: `LANG1` everywhere plus the Karabiner
   rule on the Mac; or a layer per OS (or the firmware's OS detection, if it has one) with
   `KC_CAPS` on the macOS layer. Put the bottom row on every layer.
3. **Build with the firmware project's own toolchain**, on the user's machine, after the user
   agrees to install it. The keymap source and build output stay in the user's own fork or
   folder, never in this repo.
4. **Flashing needs the user's hands** (bootloader button or key combo). Give exact steps from the
   firmware's docs and wait for them to confirm.
5. Run `07-verification.md`. With LangToggle in firmware, rows 2–7 should pass with no
   remapper running (macOS: except the LANG1 rule, if used).
