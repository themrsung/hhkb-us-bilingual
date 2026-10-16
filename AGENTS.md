# AGENTS.md — HHKB US-layout bilingual setup (any OS)

Read this file first. It is the entry point for any agent asked to set up this keyboard layout on
a machine. It covers the layout, the order of operations, and what "done" looks like. The
detailed steps are in `docs/`:

| File | Covers |
| --- | --- |
| [`docs/01-hardware.md`](docs/01-hardware.md) | HHKB US models, DIP switches, stock bottom row, USB IDs |
| [`docs/02-layout-spec.md`](docs/02-layout-spec.md) | **The OS-neutral spec**: roles (System / Aux / LangToggle) and what each OS maps them to |
| [`docs/03-macos.md`](docs/03-macos.md) | macOS: input sources, Caps Lock switching, Karabiner-Elements and the example rule file |
| [`docs/04-windows.md`](docs/04-windows.md) | Windows: Korean IME, 한/영, building the config for AutoHotkey or PowerToys |
| [`docs/05-other-os.md`](docs/05-other-os.md) | Linux, Android, iPadOS, ChromeOS: how to turn the spec into native config |
| [`docs/06-customization.md`](docs/06-customization.md) | Other languages, other bottom-row layouts |
| [`docs/07-verification.md`](docs/07-verification.md) | Acceptance checklist and troubleshooting |

Example config files: [`karabiner/`](karabiner/) (macOS only, drag-and-drop complex
modifications for Karabiner-Elements).

**No binaries, no scripts, on purpose.** The repo is instructions plus example config. Don't add
installers, helper executables, or a config framework. On an OS without example files, write the
native tool's config yourself from `docs/02-layout-spec.md`, show it to the user, and apply it.

**Scope:**
- **HHKB US (ANSI) layout.** If the user has a **JIS** HHKB, point them to
  <https://github.com/themrsung/hhkb-ekj-trilingual> and ask before continuing here.
- **Any OS**, but only macOS has example files. On other OSes, tell the user it's untested and
  name the tool you'll use before installing anything.
- This repo is AI-generated and not serviced. Follow the docs at the ref the user gave you
  (normally a release tag), not `main`.
- **Planning status (v0.x):** facts marked `TODO(hardware)` haven't been checked on a real board.
  When you hit one, check it on the user's machine (EventViewer, device list, the manual) instead
  of trusting the doc, and tell the user what you found.

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

## 2. Hardware first, software second

Get as close to the target row as possible with the **DIP switches** (and, if needed, PFU's
keymap tool), so the software layer does as little as possible.

On a stock HHKB US the bottom row is `Alt | ◇ | Space | ◇ | Alt`. ◇ sends the HID GUI key, which
*is* the System key on every OS, and Alt *is* the Aux key. So with the default DIP settings
(`docs/01-hardware.md`) only one key needs a software remap:

| Key | Hardware sends | Target role | Software remap needed |
| --- | --- | --- | --- |
| Left Alt | `left_alt` / `left_option` | Aux | no |
| Left ◇ | `left_gui` / `left_command` | System | no |
| Space | `spacebar` | Space | no |
| **Right ◇** | `right_gui` / `right_command` | **LangToggle** | **yes** |
| Right Alt | `right_alt` / `right_option` | Aux | no |

> **Order matters: set the DIP switches BEFORE writing any keymap with the keymap tool.** On the
> JIS HYBRID Type-S, changing DIP switches afterwards overwrote the stored keymap.
> `TODO(hardware)`: confirm whether the US boards behave the same; follow this order anyway.

## 3. Order of operations

1. **Hardware:** set DIP switches, then the keymap tool only if needed (`docs/01-hardware.md`).
2. **OS input sources:** enable the two languages and the OS's native switch mechanism
   (`docs/03`, `04` or `05`).
3. **Remap right ◇ → LangToggle** with the OS's tool: Karabiner-Elements on macOS (import the
   example file), AutoHotkey / PowerToys on Windows, and so on.
4. **Verify** every row of the checklist (`docs/07-verification.md`).

## 4. Rules for agents working on this

- **Merge, don't clobber.** Back up any config before editing it
  (`cp file file.bak-$(date +%Y%m%d%H%M%S)`), and add only the HHKB pieces.
- **Scope remaps to the HHKB** (USB vendor ID `1278`, PFU Limited) wherever the tool supports
  per-device rules, so other keyboards keep their right Command / Win key.
- **Don't change the defaults** unless the user asks. They are the owner's settings.
- Things that need human hands: DIP switches, the keymap tool, approving system extensions and
  permissions, and pressing keys for the checks. Tell the user exactly what to click, and ask them
  to confirm before you continue.
- Check, don't assume: use a key-event viewer (Karabiner-EventViewer on macOS, AutoHotkey's
  KeyHistory on Windows, `wev` / `evtest` on Linux) and the OS's input indicator.
