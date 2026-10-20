# AGENTS.md — HHKB US-layout bilingual setup (any OS)

Read this file first. It is the entry point for any agent asked to set up this keyboard layout on
a machine. It covers the layout, the order of operations, and what "done" looks like. The
detailed steps are in `docs/`:

| File | Covers |
| --- | --- |
| [`docs/01-hardware.md`](docs/01-hardware.md) | HHKB US models (Pro BT, HYBRID family, Classic, Studio), the DIP → keymap tool → software ladder, DIP switches, keymap tool, raw codes, Bluetooth pairing |
| [`docs/02-layout-spec.md`](docs/02-layout-spec.md) | **The OS-neutral spec**: roles (System / Aux / LangToggle) and what each OS maps them to, in firmware and in software |
| [`docs/03-macos.md`](docs/03-macos.md) | macOS: input sources, Caps Lock switching, Karabiner-Elements and the example rule file |
| [`docs/04-windows.md`](docs/04-windows.md) | Windows: Korean IME, `LANG1` from the keymap tool, the AutoHotkey v2 script and its logon task |
| [`docs/05-other-os.md`](docs/05-other-os.md) | Linux, Android, iPadOS, ChromeOS: how to turn the spec into native config |
| [`docs/06-customization.md`](docs/06-customization.md) | Other languages, other bottom-row layouts, tap/hold |
| [`docs/07-verification.md`](docs/07-verification.md) | Acceptance checklist and troubleshooting |
| [`docs/08-advanced.md`](docs/08-advanced.md) | Advanced: fitting the layout into the user's own remapper or firmware, or building custom firmware |
| [`issues/`](issues/README.md) | Known issues carried over from the JIS repo: DIP switches resetting the keymap, Studio SW6 power saving, a US HHKB on a Mac whose Karabiner virtual keyboard is JIS, the macOS input-source desync, Windows' per-window language, inter-OS muscle memory. Check here before "fixing" something |

Example config files: [`karabiner/`](karabiner/) (macOS only, drag-and-drop complex
modifications for Karabiner-Elements). `hhkb-us-bilingual.json` is the normal one.
`hhkb-us-on-jis-virtual.json` is only for a Mac whose Karabiner virtual keyboard is JIS
(`issues/macos-jis-virtual-keyboard.md`).

**No binaries, no scripts, on purpose.** The repo is instructions plus example config. Don't add
installers, helper executables, or a config framework. On an OS without example files, write the
native tool's config yourself from `docs/02-layout-spec.md`, show it to the user, and apply it.

**Scope:**
- **HHKB US (ANSI) layout.** If the user has a **JIS** HHKB, point them to
  <https://github.com/themrsung/hhkb-ekj-trilingual> and ask before continuing here.
- **Three setup paths**, each with its own prompt in the README: **macOS** (Karabiner-Elements),
  **Windows** (AutoHotkey v2), and **Advanced** (the user's own setup, other OSes, or custom
  firmware: `docs/05-other-os.md`, `docs/08-advanced.md`). Only macOS has example files. On
  other OSes, tell the user it's untested and name the tool you'll use before installing anything.
- This repo is AI-generated and not serviced. Follow the docs at the ref the user gave you
  (normally a release tag), not `main`.
- **Planning status (v0.x):** facts marked `TODO(hardware)` haven't been checked on a real US
  board. Findings marked "JIS repo" were checked on JIS boards and are expected, not known, to
  carry over. When you hit either, check it on the user's machine (event viewer, device list, the
  manual) instead of trusting the doc, and tell the user what you found.

---

## 1. The layout

Bottom row, left to right (the HHKB US bottom row has five keys; the corners are empty):

```
Aux | System | Space | LangToggle | Aux
```

- **System**: the OS's main modifier (Command on macOS, Win on Windows, Super on Linux, Meta on
  Android).
- **Aux**: the OS's second modifier (Option on macOS, Alt elsewhere).
- **LangToggle**: switches between the two languages (default **English ⇄ Korean**). How it does
  that depends on the OS; see `docs/02-layout-spec.md`.

The key left of A stays **Control** (HHKB default; Caps Lock is Fn+Tab).

## 2. Hardware first, software last

Climb down this ladder, and stop at the first rung that gets the whole row:

1. **DIP switches** (every model).
2. **PFU's keymap tool** (HYBRID family, Classic, Studio). Align the bottom row, and put
   LangToggle on the right ◇ in firmware where the OS can use it (`LANG1` for Windows / Linux,
   Caps Lock for a macOS-only board; on the Studio, one keymap profile per OS).
3. **Software**: Karabiner-Elements on macOS, AutoHotkey v2 on Windows, the user's own tool on
   the Advanced path. Only for what rungs 1–2 couldn't do.

On a stock HHKB US the bottom row is `Alt | ◇ | Space | ◇ | Alt`. ◇ sends the HID GUI key, which
*is* the System key on every OS, and Alt *is* the Aux key. So with the default DIP settings
(`docs/01-hardware.md`) only one key needs to change:

| Key | Hardware sends | Target role | Changed by |
| --- | --- | --- | --- |
| Left Alt | `left_alt` / `left_option` | Aux | — |
| Left ◇ | `left_gui` / `left_command` | System | — |
| Space | `spacebar` | Space | — |
| **Right ◇** | `right_gui` / `right_command` | **LangToggle** | **Keymap tool** if the model has one, else **software** |
| Right Alt | `right_alt` / `right_option` | Aux | — (Windows: check it doesn't become 한/영, `docs/04-windows.md`) |

**Boards without a keymap tool** (the **HHKB Professional BT**, the Pro 2) go straight from
DIP switches to software. The author's board is a Pro BT, used over Bluetooth (and USB power,
`TODO(hardware)`: check whether its USB port carries data).

> **Order matters: set the DIP switches BEFORE writing any keymap with the keymap tool.** On the
> JIS HYBRID Type-S, changing DIP switches afterwards overwrote the stored keymap
> (`issues/dip-switch-keymap-reset.md`). `TODO(hardware)`: confirm whether the US boards behave
> the same; follow this order anyway.

## 3. Order of operations

1. **Hardware:** set DIP switches, check the raw codes, then the keymap tool if the model has one
   (`docs/01-hardware.md`). If the board is used over Bluetooth, pair it and check the codes over
   Bluetooth too.
2. **OS input sources:** enable the two languages and the OS's native switch mechanism
   (`docs/03`, `04` or `05`).
3. **Software for what's left** (usually right ◇ → LangToggle): Karabiner-Elements on macOS
   (import the example file), AutoHotkey v2 on Windows (script + logon task), the user's own tool
   on the Advanced path (`docs/08-advanced.md`). Skip it if the keymap tool already did the job.
4. **Verify** every row of the checklist (`docs/07-verification.md`).

## 4. Rules for agents working on this

- **Merge, don't clobber.** Back up any config before editing it
  (`cp file file.bak-$(date +%Y%m%d%H%M%S)`), and add only the HHKB pieces.
- **Scope remaps to the HHKB** (USB vendor ID `1278`, PFU Limited) wherever the tool supports
  per-device rules, so other keyboards keep their right Command / Win key. Over Bluetooth, check
  which IDs the board reports before relying on that scope.
- **Don't change the defaults** unless the user asks. They are the owner's settings.
- **Don't replace the key-event switch** (Caps Lock / Ctrl+Space / 한/영) with a direct
  input-source select. See `issues/macos-cjk-select-desync.md`.
- Things that need human hands: DIP switches, the keymap tool, Bluetooth pairing, approving
  system extensions and permissions, UAC prompts, flashing firmware, and pressing keys for the
  checks. Tell the user exactly what to click, and ask them to confirm before you continue.
- Check, don't assume: use a key-event viewer (Karabiner-EventViewer on macOS, AutoHotkey's
  KeyHistory on Windows, `wev` / `evtest` on Linux) and the OS's input indicator.
