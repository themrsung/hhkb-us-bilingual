# 02 — Layout spec (OS-neutral)

This is the **source of truth** for the layout. The OS docs (`03`–`05`) and the example files in
`karabiner/` are translations of this page. If they disagree, this page wins, so fix the
translation.

## Roles

| Role | Definition |
| --- | --- |
| **System** | The OS's main shortcut modifier: the one used for copy/paste, app switching, etc. |
| **Aux** | The OS's second modifier. |
| **LangToggle** | One tap switches from language A to language B, and from B back to A. It never lands in a third input source. |
| **Space** | Space. |

Defaults: language A = **English (US QWERTY)**, language B = **Korean (2-Set / 두벌식)**.

## Bottom row

```
position:  1     2        3       4            5
role:      Aux | System | Space | LangToggle | Aux
hardware:  Alt | ◇       | Space | ◇            | Alt      (stock HHKB US, recommended DIP settings)
```

Positions 1, 2, 3 and 5 come from the hardware. Position 4 (right ◇) gets LangToggle from the
first rung that can do it (`01-hardware.md`):

1. **DIP switches**: keep the stock row. They can't make a LangToggle key.
2. **Keymap tool** (HYBRID family, Classic, Studio): right ◇ → a code the OS understands without
   software (`LANG1` for Windows / Linux, Caps Lock for macOS). On the Studio, one profile per OS.
3. **Software**: remap right ◇ (or the firmware code from rung 2) to the OS's LangToggle. The
   Pro BT and other boards without a keymap tool always need this rung.

Scope software rules to the HHKB (USB vendor `1278`) when the tool can do per-device rules, so
other keyboards keep their right System key.

The key left of A stays Control. Nothing else on the board changes.

## Per-OS mapping

| OS | System | Aux | LangToggle is sent as | Firmware code for right ◇ (rung 2) | Software for right ◇ (rung 3) | Doc |
| --- | --- | --- | --- | --- | --- | --- |
| **macOS** | Command | Option | `caps_lock`, with "Use the Caps Lock key to switch to and from ABC" on | Caps Lock (macOS-only boards), or `LANG1` + a Karabiner rule | Karabiner-Elements, example rules in [`karabiner/`](../karabiner/) | [`03-macos.md`](03-macos.md) |
| **Windows** | Win | Alt | 한/영 in the Microsoft Korean IME (`VK_HANGUL`, 0x15, or the IME's 한 / 영 state set directly) | `LANG1` (native 한/영, no software) | AutoHotkey v2 | [`04-windows.md`](04-windows.md) |
| **Linux** | Super | Alt | `Hangul` keysym, or whatever the IME (fcitx5 / IBus) uses to toggle | `LANG1` (`KEY_HANGEUL`) | keyd, or the desktop's own settings | [`05-other-os.md`](05-other-os.md) |
| **Android** | Meta | Alt | Ctrl+Space (switches the physical keyboard layout) | none known | Key-remapping app, or none | [`05-other-os.md`](05-other-os.md) |
| **iPadOS** | Command | Option | Caps Lock (with "switch to/from Latin" on), or Globe | Caps Lock | Settings → Hardware Keyboard → Modifier Keys (limited) | [`05-other-os.md`](05-other-os.md) |
| **ChromeOS** | Search/Launcher | Alt | Ctrl+Space | none known | ChromeOS keyboard settings (limited) | [`05-other-os.md`](05-other-os.md) |

Anything else, an existing remapper the user prefers (kanata, keyd, Hammerspoon, …), or custom
firmware on a replacement controller: [`08-advanced.md`](08-advanced.md).

`TODO(hardware)`: only the OSes the author actually uses will be checked. Everything else in
this table is a starting point for the agent, not a tested result.

## What a correct translation must do

1. Right ◇ tapped alone → LangToggle (A ⇄ B).
2. Left ◇ still works as System; both Alts still work as Aux, with any other key.
3. Other keyboards on the same machine are untouched (where the tool can tell keyboards apart).
4. It survives a reboot / log-in (the tool starts at login).
5. Holding right ◇ with another key doesn't need to do anything useful. It's not a modifier
   anymore. (Customizing this, e.g. "tap = LangToggle, hold = System", is in
   [`06-customization.md`](06-customization.md).)
