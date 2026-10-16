# Inter-OS muscle memory

**Applies to:** anyone using the same HHKB on both macOS and Windows.
**Status:** not caused by this setup. Expected, and it takes practice. (From the JIS repo.)

## Symptom

After switching between a Mac and a Windows PC, you reach for the wrong modifier or shortcut:
Command vs Ctrl for copy/paste, Option vs Alt, Cmd+Tab vs Alt+Tab, and so on.

## Cause

macOS and Windows use different modifiers for the same actions. This layout already puts the
roles in the same physical places on both: Aux (Option / Alt) on the outside, System (Command /
Win) next to Space, LangToggle on the right. What still differs is which key each OS expects for a
shortcut, for example Command+C on macOS against Ctrl+C on Windows. No keyboard remap fixes that
without breaking one OS's own conventions.

LangToggle is the same physical key on both OSes.

## What to do

Practice. Optionally, remap individual shortcuts per OS, but this repo deliberately doesn't. Each
OS keeps its native shortcuts.
