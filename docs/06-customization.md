# 06 — Customization

Change the defaults only when the user asks. Apply changes directly in the user's own config.
Don't add a settings file or framework to this repo.

## Other languages

LangToggle switches between two input sources. To use something other than English ⇄ Korean,
change which sources are enabled. The remap itself doesn't change.

| OS | Where |
| --- | --- |
| macOS | Enable the two sources. With the Caps Lock rule, one of them must be ABC/U.S. (or another Latin layout): Caps Lock switching always goes "to and from ABC". For two non-Latin sources, use the Ctrl+Space rule instead (`03-macos.md`) |
| Windows | Add the language and its IME. For non-Korean IMEs, send that IME's toggle instead of `vk15` (e.g. Japanese: `vk19` 半角/全角, or Alt+Shift / Win+Space to cycle languages) |
| Linux | Configure the IME's toggle key, and map right ◇ to that key |

## Other bottom rows

Any arrangement of the four roles is allowed. Prefer DIP switches over software for whatever the
hardware can already do:

| Want | Do |
| --- | --- |
| `System \| Aux \| Space \| Aux \| System`-style rows (Alt and ◇ swapped) | DIP SW5 ON, then check in the event viewer which code the LangToggle position now sends, and point the rule's `from` key at it |
| Left ◇ as Fn | DIP SW4 ON |
| LangToggle on the left side | Remap left ◇ instead of right ◇, and update the rule's `from` key |
| LangToggle in firmware (single-OS use) | PFU keymap tool, after the DIP switches (`01-hardware.md`, Step 2) |

After any change, rewrite the bottom-row table in `07-verification.md` for the user's run.

## Tap = LangToggle, hold = System

If the user wants to keep right ◇ as a modifier, use a tap/hold rule instead of a plain remap:

- **macOS / Karabiner:** `to` = `right_command`, `to_if_alone` = `caps_lock` (or Ctrl+Space).
- **Linux / keyd:** `rightmeta = overload(meta, hangeul)`.
- **Windows / AutoHotkey:** send 한/영 on `RWin up` only if no other key was pressed
  (`A_PriorKey = "RWin"`), and keep Win behavior otherwise.

`TODO(hardware)`: none of these are tested. Tap/hold adds a small delay to LangToggle.
