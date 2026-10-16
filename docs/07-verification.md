# 07 — Verification checklist and troubleshooting

Don't call the setup done until every row passes. Rows marked 👤 need the user to press keys and
tell you the result. Ask them, and don't assume. If the board is used over both USB and
Bluetooth, run rows 2–8 over **each** connection.

## Acceptance checklist

| # | Action | Expected |
| --- | --- | --- |
| 1 | List connected devices (`01-hardware.md`) | The HHKB shows up (vendor `1278` over USB; note what it reports over Bluetooth) |
| 2 | 👤 Key-event viewer, bottom row left → right, with the remap **on** | Aux, System, Space, *LangToggle output* (`caps_lock` / Ctrl+Space on macOS; 한/영 on Windows; `hangeul` on Linux), Aux |
| 3 | 👤 Left ◇ + C, then left ◇ + V in a text field | Copy / paste (System works) |
| 4 | 👤 Left Alt and right Alt with a letter | The OS's Aux behavior (macOS: Option characters, e.g. Option+E). Windows: right Alt must **not** toggle 한/영, unless the user chose that (`04-windows.md`, section 2) |
| 5 | 👤 From language A, tap LangToggle | Language B |
| 6 | 👤 From language B, tap LangToggle | Language A |
| 7 | 👤 Tap LangToggle 10 times while typing | Alternates every time, never lands in a third source, no missed taps |
| 8 | 👤 Type right after a switch (macOS: 20 switches, typing at once) | The text field types the new language. The menu bar can't be trusted for this (`../issues/macos-cjk-select-desync.md`) |
| 9 | 👤 Windows: switch to a window that was left in English (United States), tap LangToggle | Korean (AutoHotkey path), or note that Win+Space is needed (keymap-tool path) |
| 10 | 👤 Another keyboard's right System key (if one is connected) | Still the System key (only if the tool is per-device) |
| 11 | 👤 Key left of A + C in a terminal | Interrupts (Control) |
| 12 | Reboot or log out/in, repeat 5–6 | Still works (the tool starts at login; Windows: the green **H** tray icon is there) |
| 13 | Studio with one profile per OS: switch profiles, repeat 2 on each | Each profile's bottom row matches its OS |

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Bottom row sends the wrong codes before any remap | DIP switches (SW5 swaps Alt / ◇, SW4 makes left ◇ Fn) | Set them as in `01-hardware.md`, then reapply any keymap-tool keymap |
| Keymap-tool bottom row gone after flipping a DIP switch | The DIP change reset the stored keymap (seen on the JIS HYBRID Type-S) | Write the keymap again. Always DIP first (`../issues/dip-switch-keymap-reset.md`) |
| Studio: bottom row changes while Fn1 or Fn2 is held | The bottom row was only set on the Standard layer | Set it on every layer (Standard, Fn1, Fn2, Fn3) and write again |
| Studio: bottom row is PFU's stock after pairing | Followed the manual's "Fn+C, 2" step, which switches to keymap Profile2 | Switch back to the user's profile (Fn+C, then its number) |
| Studio: Bluetooth pairing times out | Pressed Fn+Ctrl+n before Fn+Q | Fn+Q first, then Fn+Ctrl+n (`01-hardware.md`, "Bluetooth") |
| Bluetooth board keeps falling asleep, first key lost | Aggressive power saving | `../issues/dip-switch-6-power-saving.md` |
| macOS: right Command still acts as Command | Rule not enabled, or "Modify events" off for the HHKB | Karabiner → Complex Modifications: enable the rule; Devices: turn on the HHKB |
| macOS: works over USB, not over Bluetooth | Over Bluetooth the board is a different device entry (JIS Studio: one combined keyboard + pointing device that Karabiner only observes), or it reports another vendor ID | Turn on "Modify events" for the Bluetooth entry; if the vendor ID differs, add it to the rule's identifiers (`03-macos.md`, "Bluetooth") |
| macOS: Caps Lock turns on Caps Lock instead of switching | "Use the Caps Lock key to switch to and from ABC" is off, or the tap was a long press | Turn the setting on (`03-macos.md`); or use the Ctrl+Space rule |
| macOS: toggle misses taps | Caps Lock switching's built-in delay | Use the Ctrl+Space rule instead |
| macOS: menu bar says Korean, the field types English (rare) | A `select_input_source`-style switch was added (Karabiner, Hammerspoon, im-select) | Go back to the key-event rules in `karabiner/` (`../issues/macos-cjk-select-desync.md`) |
| macOS: the MacBook's own keyboard types wrong symbols | It's JIS or ISO, and Karabiner sends it through the ANSI virtual keyboard | Set the built-in keyboard's device entry to `ignore: true` (`03-macos.md`) |
| macOS: the keymap tool's `LANG1` key does nothing | The LANG1 rule isn't enabled (macOS has no native use for `LANG1` with Korean) | Enable "LANG1 → Caps Lock" in Karabiner, or set right ◇ to Caps Lock in the keymap tool if the board is macOS-only |
| LangToggle lands in a third input source | More than two sources enabled | Remove the extra source, or ask the user which two they want |
| Windows: right Win opens the Start menu | AutoHotkey not running | `Start-ScheduledTask hhkb-us-bilingual`; check the logon task (`04-windows.md`) |
| Windows: AutoHotkey doesn't start after a restart | Started from `shell:startup` or a Run key (JIS repo: Windows skipped both) | Use the Task Scheduler logon task (`04-windows.md`) |
| Windows: LangToggle does nothing in one window | That window's input language isn't Korean (Windows keeps one per window), or it's an elevated (admin) window | Keymap-tool path: Win+Space to Korean. AutoHotkey path: elevated windows can't be reached (UIPI) (`../issues/windows-per-window-language.md`) |
| Windows: right Alt toggles 한/영 | The Korean layout maps Right Alt's scan code to `VK_HANGUL` | Ask the user; change the Korean hardware layout if they want Alt back (`04-windows.md`, section 2) |
| A JIS HHKB on the same machine also lost its right Command | Rule scoped by vendor only | Add the US board's `product_id` to the rule's identifiers |
| Switching between a Mac and a PC, reaching for the wrong shortcut | Not this setup: the OSes use different shortcuts | `../issues/inter-os-muscle-memory.md` |
