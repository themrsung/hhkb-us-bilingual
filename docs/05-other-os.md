# 05 — Other operating systems

No example files, none of it tested. The agent builds the config from
[`02-layout-spec.md`](02-layout-spec.md), says which tool it will use, and asks before installing
anything. The notes below are starting points.

## Linux

- **System** = Super, **Aux** = Alt: stock, nothing to do.
- **Input method:** fcitx5 + `fcitx5-hangul`, or IBus + `ibus-hangul`. Set its toggle key to
  `Hangul` (or to the key you choose below).
- **Right ◇ → LangToggle:** [keyd](https://github.com/rvaiya/keyd) works under X11, Wayland and the
  console, and can match the HHKB by vendor/product ID. A minimal `/etc/keyd/hhkb.conf`:

  ```ini
  [ids]
  04fe:*

  [main]
  rightmeta = hangeul
  ```

  `TODO(hardware)`: check the key name (`hangeul`) against `keyd list-keys`, fill in the product
  ID, and check the IME reacts to it. If the IME doesn't see `Hangul`, map to the IME's own toggle
  combo instead (e.g. `C-space`).
- Check: `sudo keyd monitor`, or `wev` (Wayland) / `xev` (X11).

## Android

- Physical keyboards switch layout / language with **Ctrl+Space**. Gboard and Samsung Keyboard
  follow the active language.
- Remapping right Meta → Ctrl+Space needs a key-remapping app (e.g. Key Mapper). Without one, the
  user presses Ctrl+Space by hand. `TODO(hardware)`: check whether Android sees the HHKB's right ◇
  as `META_RIGHT`.

## iPadOS

- Settings → General → Keyboard → Hardware Keyboard: turn on **Caps Lock to switch to/from
  Latin**, and add Korean.
- Settings → General → Keyboard → Hardware Keyboard → **Modifier Keys** can remap Command, but only
  both Commands together. A right-Command-only remap isn't possible. Tell the user the closest
  option is LangToggle via Fn+Tab (Caps Lock) or Globe, with the row left as it is.

## ChromeOS

- Search/Launcher = System, Alt = Aux.
- **Ctrl+Space** switches to the previous input method. ChromeOS keyboard settings can remap
  modifiers but not right Search alone. `TODO(hardware)`: check on a current ChromeOS build.
