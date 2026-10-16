# 07 — Verification checklist and troubleshooting

Don't call the setup done until every row passes. Rows marked 👤 need the user to press keys and
tell you the result. Ask them, and don't assume.

## Acceptance checklist

| # | Action | Expected |
| --- | --- | --- |
| 1 | List connected devices (`01-hardware.md`) | The HHKB shows up with vendor `1278` |
| 2 | 👤 Key-event viewer, bottom row left → right, with the remap **on** | Aux, System, Space, *LangToggle output* (`caps_lock` / Ctrl+Space / `vk15` / `hangeul`), Aux |
| 3 | 👤 Left ◇ + C, then left ◇ + V in a text field | Copy / paste (System works) |
| 4 | 👤 Left Alt and right Alt with a letter | The OS's Aux behavior (macOS: Option characters, e.g. Option+E) |
| 5 | 👤 From language A, tap LangToggle | Language B |
| 6 | 👤 From language B, tap LangToggle | Language A |
| 7 | 👤 Tap LangToggle 10 times while typing | Alternates every time, never lands in a third source, no missed taps |
| 8 | 👤 Another keyboard's right System key (if one is connected) | Still the System key (only if the tool is per-device) |
| 9 | 👤 Key left of A + C in a terminal | Interrupts (Control) |
| 10 | Reboot or log out/in, repeat 5–6 | Still works (the tool starts at login) |

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Bottom row sends the wrong codes before any remap | DIP switches (SW5 swaps Alt / ◇, SW4 makes left ◇ Fn) | Set them as in `01-hardware.md`, then reapply any keymap-tool keymap |
| macOS: right Command still acts as Command | Rule not enabled, or "Modify events" off for the HHKB | Karabiner → Complex Modifications: enable the rule; Devices: turn on the HHKB |
| macOS: Caps Lock turns on Caps Lock instead of switching | "Use the Caps Lock key to switch to and from ABC" is off, or the tap was a long press | Turn the setting on (`03-macos.md`); or use the Ctrl+Space rule |
| macOS: toggle misses taps | Caps Lock switching's built-in delay | Use the Ctrl+Space rule instead |
| LangToggle lands in a third input source | More than two sources enabled | Remove the extra source, or ask the user which two they want |
| Windows: right Win opens the Start menu | Remap not running | Start the AutoHotkey script / PowerToys; add it to startup |
| A JIS HHKB on the same machine also lost its right Command | Rule scoped by vendor only | Add the US board's `product_id` to the rule's identifiers |
