# 04 — Windows

No example files. The agent builds the config from [`02-layout-spec.md`](02-layout-spec.md) and
tells the user it's untested. `TODO(hardware)`: check every step here on a real Windows machine.

| Role | Windows | Comes from |
| --- | --- | --- |
| Aux | Alt | Hardware (stock Alt) |
| System | Win | Hardware (stock ◇) |
| LangToggle | 한/영 (`VK_HANGUL`, 0x15) | Software remap of right Win |

## 1. Input languages

Settings → Time & language → Language & region:

1. Keep **English (United States)** with the **US** keyboard.
2. Add **Korean** (한국어) → keyboard **Microsoft IME**.

A US keyboard has no 한/영 key, so without a remap the IME can only be toggled with Right Alt or
Shift+Space, depending on IME settings. `TODO(hardware)`: check what Microsoft IME → Hardware
keyboard layout settings offer on current Windows, and whether one of them makes a remap
unnecessary.

## 2. Remap right Win → 한/영

Pick one, and tell the user which one you chose before installing anything.

**AutoHotkey v2** (most control; runs as a script at login). The whole rule:

```ahk
#Requires AutoHotkey v2.0
RWin::Send "{vk15}"   ; right Win → 한/영 (VK_HANGUL)
```

Put it in a `.ahk` file and add a shortcut to it in `shell:startup` so it runs at login.
AutoHotkey can't tell keyboards apart, so **this affects every keyboard's right Win key.** Tell the
user.

**PowerToys Keyboard Manager** (GUI, no script): Remap a key → `Win (Right)` → `IME Kana`
(same virtual key, 0x15 = Kana/Hangul). Also global across keyboards. `TODO(hardware)`: check the
exact name PowerToys shows for 0x15.

Don't use a registry `Scancode Map` unless the user asks. It needs a reboot, can't be scoped to
the HHKB, and is easy to leave behind.

## 3. Check

- AutoHotkey: tray icon → Open → View → Key history. Pressing right ◇ should show `RWin` coming in
  and `vk15` going out.
- The language indicator in the taskbar should switch between `A` and `가` on each tap.
