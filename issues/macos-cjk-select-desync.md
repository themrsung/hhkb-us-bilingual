# macOS: Korean selected, field still types English

**Applies to:** macOS, **only if** something selects the input source directly: Karabiner's
`select_input_source`, Hammerspoon's `hs.keycodes.currentSourceID(...)`, im-select, issw, and
similar tools. This repo's default rules don't.
**Status:** a macOS bug, unfixed. Measured in the JIS repo (`docs/04-hammerspoon.md` there,
"Known issue"). This repo avoids it by design.

## Symptom

About 1 switch in 100, the menu bar (and any "current input source" query) says 2-Set Korean, but
the text field you're typing in keeps the previous source until focus changes.

## Cause

`TISSelectInputSource`, called from a background process, sometimes changes the system-wide source
without reattaching the focused app's text input client. It's worse when typing starts within
about 50 ms of the switch. Reported in
[Karabiner-Elements #1602](https://github.com/pqrs-org/Karabiner-Elements/issues/1602) and on the
[Apple Developer Forums (#748791)](https://developer.apple.com/forums/thread/748791).

Key events that macOS handles itself (Caps Lock switching, the "Select the previous input source"
shortcut, the JIS 英数 / かな keys) go through the system's own switching path, and weren't seen
to desync.

## What this repo does

- LangToggle is a **key event**: Caps Lock with "Use the Caps Lock key to switch to and from ABC",
  or Ctrl+Space with "Select the previous input source" (`../docs/03-macos.md`). Don't replace it
  with `select_input_source` or a Hammerspoon selector.
- A "check and retry" can't catch this failure: in the JIS measurements the check read Korean every
  time. Focus-bounce workarounds dropped the first keystroke (60 of 60 trials). Don't add either.
- If a user really wants a one-way "always Korean" key (like the JIS repo's HHKB key), tell them
  about this bug first, and use the JIS repo's Hammerspoon handler as the starting point.

To test any switching method, do a few hundred switches with typing right after each, then read
back what landed. A ~1% rate can't be judged from a handful of presses.
