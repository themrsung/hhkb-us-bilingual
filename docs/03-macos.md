# 03 — macOS

| Role | macOS | Comes from |
| --- | --- | --- |
| Aux | Option | Hardware (stock Alt) |
| System | Command | Hardware (stock ◇) |
| LangToggle | Caps Lock → switches ABC ⇄ Korean 2-Set | The first rung that can do it: keymap tool (Caps Lock or `LANG1` on right ◇), else Karabiner-Elements (right Command → `caps_lock`). Plus a macOS setting |

`TODO(hardware)`: none of this has been run against a US HHKB yet. Findings marked "JIS repo"
were checked on JIS boards in
[hhkb-ekj-trilingual](https://github.com/themrsung/hhkb-ekj-trilingual) and are expected, not
known, to carry over.

## Which path

| Board | Right ◇ from the keymap tool (`01-hardware.md`, Step 2) | Karabiner needed? | Rule to enable |
| --- | --- | --- | --- |
| **Pro BT**, Pro 2 (no keymap tool) | stock (`right_command`) | **Yes** | right Command → Caps Lock **[default]** |
| HYBRID / Classic / Studio, used only with macOS (or Studio with a macOS profile) | **Caps Lock** | **No** | none. Do sections 1–2 and stop |
| HYBRID / Classic, shared with Windows | **`LANG1`** (`japanese_kana`) | Yes | LANG1 → Caps Lock **[keymap-tool boards]** |
| Any board, if the tool step was skipped | stock | Yes | right Command → Caps Lock **[default]** |
| Any board, on a Mac whose Karabiner virtual keyboard is **JIS** (shared with a JIS HHKB) | stock | **No**: keep the US HHKB ignored | none. Ctrl+Space via "Select the previous input source" (`../issues/macos-jis-virtual-keyboard.md`) |

## 1. Keyboard type

When macOS asks to identify the new keyboard (Keyboard Setup Assistant), pick **ANSI**. To change
it later: System Settings → Keyboard → Keyboard Type / Change Keyboard Type… → ANSI.

Check:

```sh
defaults read /Library/Preferences/com.apple.keyboardtype
# the HHKB's entry ("<product>-1278-0") must be 40 (ANSI). 41 = ISO, 42 = JIS
```

JIS repo: the JIS Studio over Bluetooth reused its USB entry (`21-1278-0`) and got no separate
Bluetooth entry. The Pro BT, paired over Bluetooth on the author's Mac, has **no entry at all**
(no `514-1278-0`). `TODO(hardware)`: check whether macOS then treats it as ANSI, and whether
Keyboard Setup Assistant can add the entry.

## 2. Input sources

System Settings → Keyboard → Text Input → Input Sources → **Edit…**:

1. Make sure **ABC** (or **U.S.**) is enabled.
2. **+** → Korean → **2-Set Korean** (`com.apple.inputmethod.Korean.2SetKorean`).
3. Remove any other input sources the user doesn't want, so LangToggle only goes between the two.
   (Ask first; don't remove the user's sources yourself.)
4. Turn on **"Use the Caps Lock key to switch to and from ABC"** (on some macOS versions: "…to and
   from the last used Latin input source").

With that setting on, a **short tap** of Caps Lock switches ABC ⇄ the last non-ABC source (Korean
2-Set), and a **long press** still toggles real Caps Lock.

Check the current source: the input menu in the menu bar, or

```sh
defaults read com.apple.HIToolbox AppleSelectedInputSources
```

**Why a key event and not `select_input_source`:** Caps Lock (and Ctrl+Space) are real key events
that macOS handles itself. Selecting Korean from a background tool (Karabiner's
`select_input_source`, Hammerspoon, im-select, …) hits a macOS bug about 1 time in 100: the menu
bar says Korean, but the focused text field keeps typing English. Don't switch this setup to
`select_input_source`. See [`../issues/macos-cjk-select-desync.md`](../issues/macos-cjk-select-desync.md).

## 3. Karabiner-Elements

Skip this section if the keymap tool already put Caps Lock on right ◇ (see "Which path").

```sh
brew install --cask karabiner-elements
```

Or download the `.dmg` from <https://karabiner-elements.pqrs.org/>. Then the **user** must:

1. Open Karabiner-Elements and follow its setup prompts.
2. System Settings → General → **Login Items & Extensions** → Driver Extensions → allow
   **Karabiner-DriverKit-VirtualHIDDevice**.
3. System Settings → Privacy & Security → **Input Monitoring** → allow the Karabiner entries
   (`karabiner_grabber` / `karabiner_observer`, or Karabiner-Core-Service on newer versions).
4. Karabiner-Elements → **Devices**: turn on "Modify events" for the HHKB, **for every entry it
   shows up as** (see "Bluetooth" below).
5. Karabiner-Elements → **Virtual Keyboard**: keyboard type **ANSI**, unless the Mac already uses
   JIS for a JIS HHKB (see "Virtual keyboard type" below).

### Import the example rules (drag and drop)

The example file is [`karabiner/hhkb-us-bilingual.json`](../karabiner/hhkb-us-bilingual.json). It's
a Karabiner "complex modifications" file. Installing it doesn't touch `karabiner.json` until the
user enables a rule.

```sh
mkdir -p ~/.config/karabiner/assets/complex_modifications
cp karabiner/hhkb-us-bilingual.json ~/.config/karabiner/assets/complex_modifications/
```

(Or drag the file into that folder in Finder: ⌘⇧G → `~/.config/karabiner/assets/complex_modifications`.)

Then Karabiner-Elements → **Complex Modifications** → **Add predefined rule** → "HHKB US bilingual"
→ enable **one** of:

| Rule | From | Sends | Use when |
| --- | --- | --- | --- |
| **right Command → Caps Lock** [default] | `right_command` | `caps_lock` | Stock right ◇ (Pro BT, Pro 2, or the keymap step was skipped). Needs the setting from section 2, step 4 |
| right Command → Ctrl+Space [alternative] | `right_command` | Ctrl+Space | Caps Lock switching misbehaves (missed taps, lag). Needs "Select the previous input source" = Ctrl+Space in System Settings → Keyboard → Keyboard Shortcuts → Input Sources |
| LANG1 → Caps Lock [keymap-tool boards] | `japanese_kana` (HID `LANG1`) | `caps_lock` | The keymap tool set right ◇ to `LANG1` so Windows gets 한/영 natively (`01-hardware.md`, Step 2). Needs the setting from section 2, step 4 |

**Don't enable both right Command rules.** They catch the same key. The LANG1 rule catches a
different key and only matters on keymap-tool boards.

All rules are scoped to `vendor_id 1278` (any PFU keyboard), so other keyboards keep their right
Command. If a JIS HHKB is used on the same Mac, add the US board's `"product_id"` to the
identifiers so the JIS board keeps its right Command. Pro BT: `514`, over Bluetooth, with the same
vendor ID `1278` (`01-hardware.md`). `TODO(hardware)`: product IDs of the other US models.

### Bluetooth

From the JIS repo, checked on a JIS Studio:

- **Over USB** the Studio shows up three times (keyboard, pointing device, consumer). Only the
  keyboard entry needs "Modify events".
- **Over Bluetooth** it shows up **once**, as `HHKB-Studio<n>`, a combined keyboard + pointing
  device. Karabiner doesn't grab pointing devices by default, so it only *observes* this one and
  **no rule fires**. Fix: turn on "Modify events" for that entry (in `karabiner.json`: a
  `devices` entry with `"is_keyboard": true, "is_pointing_device": true, "vendor_id": 1278,
  "product_id": <id>` and `"ignore": false`). The pointing stick and gesture pads keep working
  through Karabiner. `/var/log/karabiner/core_service.log` shows `… monitor is started
  (grabbed)` once it's in place.
- The number in `HHKB-Studio<n>` is the pairing slot. Match on vendor / product ID, not the name.

The **Pro BT** shows up **once**, as `HHKB-BT`: `is_keyboard` only, `vendor_id 1278`,
`product_id 514`, transport `Bluetooth`. It isn't a pointing device, so the Studio fix above isn't
needed. Turning on "Modify events" for that one entry is enough (in `karabiner.json`: a `devices`
entry with `"is_keyboard": true, "vendor_id": 1278, "product_id": 514` and `"ignore": false`).
`TODO(hardware)`: check that the rule fires over Bluetooth, and how the HYBRID shows up.

### Built-in keyboard that isn't ANSI

Karabiner sends every keyboard it grabs through **one** virtual keyboard, with one keyboard type
(no per-device type in Karabiner-Elements 16.3.0). With the virtual keyboard set to ANSI, a grabbed
**JIS or ISO** built-in keyboard (a JIS or ISO MacBook) types some symbols wrong. JIS repo: the
mirror case (JIS virtual keyboard, US MacBook) was seen on a MacBook Air (M4).

Fix: let Karabiner ignore the built-in keyboard, so macOS reads it natively. First check that no
enabled Karabiner rule needs it, because ignoring it turns those off.

```sh
# Apple silicon: the built-in keyboard is {"is_keyboard": true} with no vendor/product ID.
# Intel Macs report vendor_id 1452 and a product_id; use those instead.
KJ=~/.config/karabiner/karabiner.json
cp "$KJ" "$KJ.bak-$(date +%Y%m%d%H%M%S)"
jq '.profiles |= map(if .selected then
      .devices = (if any((.devices // [])[]; .identifiers == {"is_keyboard": true})
                  then .devices | map(if .identifiers == {"is_keyboard": true} then .ignore = true else . end)
                  else (.devices // []) + [{ "identifiers": { "is_keyboard": true }, "ignore": true }] end)
    else . end)' "$KJ" > "$KJ.tmp" && mv "$KJ.tmp" "$KJ"
```

A US built-in keyboard needs nothing: it matches the ANSI virtual keyboard.

### Virtual keyboard type on a Mac shared with a JIS HHKB

The author's MacBook Air (M4) has a US built-in keyboard, but its Karabiner virtual keyboard is
**JIS**, for the JIS HHKBs set up by hhkb-ekj-trilingual. The built-in keyboard is set to
`ignore: true`, so macOS reads it natively. The US HHKB can't be ignored the same way when it
needs the right Command rule: Karabiner then sends it through the JIS virtual keyboard.
Its symbol keys would then type the JIS legends. So on such a Mac, **don't** turn on "Modify
events" for the US HHKB and **don't** switch the virtual keyboard to ANSI without asking: the JIS
boards on the same Mac depend on it. Keep the US HHKB ignored and use Ctrl+Space for LangToggle, or
add per-key translation rules first: the untested example
[`../karabiner/hhkb-us-on-jis-virtual.json`](../karabiner/hhkb-us-on-jis-virtual.json). See
[`../issues/macos-jis-virtual-keyboard.md`](../issues/macos-jis-virtual-keyboard.md).

### Notes

- `from.modifiers.optional: ["any"]` means right Command is LangToggle even while Shift etc. is held.
  Right Command is no longer a modifier on the HHKB. Left Command still is.
- An agent may instead merge the same manipulator into `~/.config/karabiner/karabiner.json`
  (`profiles[].complex_modifications.rules`) directly. Back the file up first, and don't add a
  rule that's already there.
