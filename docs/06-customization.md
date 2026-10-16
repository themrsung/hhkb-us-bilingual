# 06 — Customization

Change the defaults only when the user asks. Apply changes directly in the user's own config.
Don't add a settings file or framework to this repo.

## Other languages

LangToggle switches between two input sources. To use something other than English ⇄ Korean,
change which sources are enabled. The remap itself doesn't change.

| OS | Where |
| --- | --- |
| macOS | Enable the two sources. With the Caps Lock rule, one of them must be ABC/U.S. (or another Latin layout): Caps Lock switching always goes "to and from ABC". For two non-Latin sources, use the Ctrl+Space rule instead (`03-macos.md`) |
| Windows | Add the language and its IME. For a non-Korean IME, change `HKL_KO` / `LANG_KO` and the 한 / 영 targets in the AutoHotkey script (`04-windows.md`) to that IME's language and on/off state. The JIS repo's script (`docs/windows/03-autohotkey.md` there) shows the Japanese values. `LANG1` from the keymap tool is 한/영 only in Korean |
| Linux | Configure the IME's toggle key, and map right ◇ to that key |

## Other bottom rows

Any arrangement of the four roles is allowed. Prefer DIP switches over software for whatever the
hardware can already do:

| Want | Do |
| --- | --- |
| `System \| Aux \| Space \| Aux \| System`-style rows (Alt and ◇ swapped) | DIP SW5 ON, then check in the event viewer which code the LangToggle position now sends, and point the rule's `from` key at it |
| Left ◇ as Fn | DIP SW4 ON (Pro BT / HYBRID family / Classic; not the Studio) |
| LangToggle on the left side | Remap left ◇ instead of right ◇, and update the rule's `from` key |
| LangToggle in firmware | PFU keymap tool, after the DIP switches (`01-hardware.md`, Step 2). This is the default rung for boards the tool supports |
| HHKB Studio scroll direction | DIP SW4 ON (JIS repo: flipping it either way didn't reset the keymap) |

After any change, rewrite the bottom-row table in `07-verification.md` for the user's run.

## Tap = LangToggle, hold = System

If the user wants to keep right ◇ as a modifier, use a tap/hold rule instead of a plain remap:

- **macOS / Karabiner:** `to` = `right_command`, `to_if_alone` = `caps_lock` (or Ctrl+Space).
- **Linux / keyd:** `rightmeta = overload(meta, hangeul)`.
- **Windows / AutoHotkey:** replace `RWin::LangToggle()` in the script (`04-windows.md`) with

  ```autohotkey
  ~RWin::Send("{Blind}{vkE8}")                     ; mask key: a lone tap won't open Start
  ~RWin up::(A_PriorKey = "RWin") && LangToggle()  ; tap alone = LangToggle; Win+key = Win
  ```

`TODO(hardware)`: none of these are tested. Tap/hold adds a small delay to LangToggle.
