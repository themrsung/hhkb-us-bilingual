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

Only position 4 needs a software rule. Scope that rule to the HHKB (USB vendor `1278`) when the
tool can do per-device rules, so other keyboards keep their right System key.

The key left of A stays Control. Nothing else on the board changes.

## Per-OS mapping

| OS | System | Aux | LangToggle is sent as | Tool for the right ◇ remap | Example files |
| --- | --- | --- | --- | --- | --- |
| **macOS** | Command | Option | `caps_lock`, with "Use the Caps Lock key to switch to and from ABC" on | Karabiner-Elements | [`karabiner/`](../karabiner/) ([`03-macos.md`](03-macos.md)) |
| **Windows** | Win | Alt | 한/영 (`VK_HANGUL`, 0x15) to the Microsoft Korean IME | AutoHotkey v2 or PowerToys Keyboard Manager | none ([`04-windows.md`](04-windows.md)) |
| **Linux** | Super | Alt | `Hangul` keysym, or whatever the IME (fcitx5 / IBus) uses to toggle | keyd (any session), or the desktop's own settings | none ([`05-other-os.md`](05-other-os.md)) |
| **Android** | Meta | Alt | Ctrl+Space (switches the physical keyboard layout) | Key-remapping app, or none | none |
| **iPadOS** | Command | Option | Caps Lock (with "switch to/from Latin" on), or Globe | Settings → Hardware Keyboard → Modifier Keys (limited) | none |
| **ChromeOS** | Search/Launcher | Alt | Ctrl+Space | ChromeOS keyboard settings (limited) | none |

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
