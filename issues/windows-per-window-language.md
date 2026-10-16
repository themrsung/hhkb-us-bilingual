# Windows: input language is per window

**Applies to:** Windows (`../docs/04-windows.md`).
**Status:** Windows behavior, not a bug. Seen in the JIS repo. Handled by the AutoHotkey script;
needs care on the keymap-tool path.

## Symptom

LangToggle works in one window and seems dead in another. Or a window "switches to English on its
own".

## Cause

Windows keeps the **input language per window**. A window can be in English (United States) while
another is in Korean. 한/영 (`VK_HANGUL`, which is what the keymap tool's `LANG1` becomes) only acts
inside the Korean IME, so in a window whose language is English it does nothing. Win+Space, or an
app that sets its own language, changes a window's language. JIS repo: the exact trigger for the
"on its own" case wasn't pinned down.

On a US board this changes nothing about symbols: English (US) and the Korean IME both use the US
layout. (In the JIS repo it also swapped the symbol layout, which doesn't apply here.)

## What to do

- **AutoHotkey path:** nothing. The script pulls a window that isn't in Korean into Korean (한) on
  the first tap.
- **Keymap-tool path (`LANG1`, no AutoHotkey):** put Korean first in the language list, and
  optionally remove the English (United States) keyboard so there's no other language to land in
  (ask the user first). To recover a window, press Win+Space to Korean once.
- **Elevated (admin) windows** can't be switched by a non-elevated AutoHotkey (UIPI). The
  keymap-tool path doesn't have this limit.
