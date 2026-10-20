# Known issues

Issues carried over from the JIS sibling repo,
[hhkb-ekj-trilingual](https://github.com/themrsung/hhkb-ekj-trilingual) (`issues/` and the
troubleshooting notes in `docs/` there), where they apply to a US board. They were seen on JIS
boards. `TODO(hardware)`: confirm each one on a US board, and record the result in the file.

These aren't bugs to "fix" in the configs. Read the one that matches before changing anything.

| Issue | Applies to | In short |
| --- | --- | --- |
| [DIP switches can reset the keymap](dip-switch-keymap-reset.md) | HYBRID family (seen on JIS); Studio (SW4 / SW6 tested safe on JIS) | Set DIP switches first, then the keymap tool. Recheck the keymap after any DIP change |
| [DIP switch 6: power saving](dip-switch-6-power-saving.md) | HHKB Studio over Bluetooth (seen on JIS); maybe the HYBRID family. Not the Pro BT (SW6 = Wake Up) | Studio SW6 is inverted (OFF = power saving on). If the board keeps sleeping, flip SW6 ON |
| [macOS: US HHKB on a Mac whose Karabiner virtual keyboard is JIS](macos-jis-virtual-keyboard.md) | macOS shared with a JIS HHKB (the author's Mac) | Grabbing the US board makes it type JIS legends. Keep it ignored and use Ctrl+Space, or translate every differing key first |
| [macOS: Korean selected, field still types English](macos-cjk-select-desync.md) | macOS, if anything selects the input source directly | Why this repo switches with a key event (Caps Lock / Ctrl+Space) and never with `select_input_source` |
| [Windows: input language is per window](windows-per-window-language.md) | Windows | 한/영 only works in a window whose language is Korean |
| [Inter-OS muscle memory](inter-os-muscle-memory.md) | macOS + Windows users | Not caused by this setup. Switching between the two OSes takes practice |
