# 03 — macOS

| Role | macOS | Comes from |
| --- | --- | --- |
| Aux | Option | Hardware (stock Alt) |
| System | Command | Hardware (stock ◇) |
| LangToggle | Caps Lock → switches ABC ⇄ Korean 2-Set | Karabiner-Elements (right Command → `caps_lock`) + a macOS setting |

`TODO(hardware)`: none of this has been run against a US HHKB yet.

## 1. Keyboard type

When macOS asks to identify the new keyboard (Keyboard Setup Assistant), pick **ANSI**. To change
it later: System Settings → Keyboard → Keyboard Type / Change Keyboard Type… → ANSI.

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

## 3. Karabiner-Elements

```sh
brew install --cask karabiner-elements
```

Or download the `.dmg` from <https://karabiner-elements.pqrs.org/>. Then the **user** must:

1. Open Karabiner-Elements and follow its setup prompts.
2. System Settings → General → **Login Items & Extensions** → Driver Extensions → allow
   **Karabiner-DriverKit-VirtualHIDDevice**.
3. System Settings → Privacy & Security → **Input Monitoring** → allow the Karabiner entries.
4. Karabiner-Elements → **Devices**: turn on "Modify events" for the HHKB.
5. Karabiner-Elements → **Virtual Keyboard**: keyboard type **ANSI**.

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

| Rule | Sends | Use when |
| --- | --- | --- |
| **right Command → Caps Lock** (default) | `caps_lock` | Normal case. Needs the setting from section 2, step 4 |
| right Command → Ctrl+Space (alternative) | Ctrl+Space | Caps Lock switching misbehaves (missed taps, lag). Needs "Select the previous input source" = Ctrl+Space in System Settings → Keyboard → Keyboard Shortcuts → Input Sources |

**Don't enable both.** They catch the same key.

Both rules are scoped to `vendor_id 1278` (any PFU keyboard), so other keyboards keep their right
Command. `TODO(hardware)`: once the product ID is known, add `"product_id": <id>` to the
identifiers so a JIS HHKB on the same Mac isn't affected.

### Notes

- `from.modifiers.optional: ["any"]` means right Command is LangToggle even while Shift etc. is held.
  Right Command is no longer a modifier on the HHKB. Left Command still is.
- An agent may instead merge the same manipulator into `~/.config/karabiner/karabiner.json`
  (`profiles[].complex_modifications.rules`) directly. Back the file up first, and don't add a
  rule that's already there.
