# macOS: US HHKB on a Mac whose Karabiner virtual keyboard is JIS

**Applies to:** macOS, when the same Mac also runs a **JIS** HHKB through Karabiner-Elements (for
example with [hhkb-ekj-trilingual](https://github.com/themrsung/hhkb-ekj-trilingual)), so the
Karabiner virtual keyboard type is **JIS**.
**Status:** the author's setup (Pro BT `HHKB-BT`, product ID `514`, over Bluetooth; MacBook Air
M4 with a US built-in keyboard; Karabiner-Elements 16.3.0; macOS 27). The differences are measured
(below). No fix built yet.

## Symptom

Once Karabiner modifies the US HHKB, its symbol keys type what's printed on a **JIS** board, not
what's printed on the US keycaps. For example Shift+2 types `"` instead of `@`. Letters, digits,
`-`, `;`, `` ` ``, `~`, `,` `.` `/` and their shifted symbols `<` `>` `?` aren't affected.

### Measured (2026-10-20)

Pro BT over Bluetooth, typed in TextEdit, in ABC and in 2-Set Korean, first with the HHKB ignored
by Karabiner (native), then with "Modify events" on (through the JIS virtual keyboard). Native, the
HHKB typed every US legend correctly in both input sources. Through the JIS virtual keyboard, only
these keys changed:

| US key | Native (correct) | Through JIS virtual keyboard | Send instead (the key that typed this character in the same test) |
| --- | --- | --- | --- |
| `=` | `=` | `^` | Shift+`-` (`hyphen`) |
| `\` | `\` | `]` | Option+¥ (`international3` + Option), as the JIS repo's ろ rule does. **Inferred** |
| `[` | `[` | `@` | `]` key (`close_bracket`) |
| `]` | `]` | `[` | `\` key (`backslash`) |
| `'` | `'` | `:` | Shift+`7` |
| Shift+`2` | `@` | `"` | `[` key (`open_bracket`), no Shift |
| Shift+`6` | `^` | `&` | `=` key (`equal_sign`), no Shift |
| Shift+`7` | `&` | `'` | Shift+`6` |
| Shift+`8` | `*` | `(` | Shift+`'` (`quote`) |
| Shift+`9` | `(` | `)` | Shift+`8` |
| Shift+`0` | `)` | `0` | Shift+`9` |
| Shift+`-` | `_` | `=` | Shift+ろ (`international1` + Shift). **Inferred** |
| Shift+`=` | `+` | `~` | Shift+`;` (`semicolon`) |
| Shift+`\` | `\|` | `}` | Shift+¥ (`international3` + Shift). **Inferred** |
| Shift+`[` | `{` | `` ` `` | Shift+`]` key (`close_bracket`) |
| Shift+`]` | `}` | `{` | Shift+`\` key (`backslash`) |
| Shift+`;` | `:` | `+` | `'` key (`quote`), no Shift |
| Shift+`'` | `"` | `*` | Shift+`2` |

The US board has no ¥ or ろ key, so the three **inferred** rows couldn't be measured.
`TODO(hardware)`: check them once the rules are in place.

2-Set Korean typed the same characters as ABC, except that Korean turns every `` ` `` into `₩`.
That happens natively too, on the built-in keyboard as well as the HHKB, so it isn't part of this
issue.

## Cause

Karabiner sends every keyboard it grabs through **one** virtual keyboard with **one** keyboard
type. Karabiner-Elements 16.3.0 has no per-device keyboard type. macOS then reads the US board's
key codes with the JIS keyboard type, and the ABC layout on a JIS keyboard type follows the JIS
legends. The virtual keyboard has to stay JIS for the JIS HHKBs on the same Mac.

A device Karabiner **ignores** (`"ignore": true`) bypasses the virtual keyboard, and macOS reads it
natively with the right legends. That's how the author's US built-in keyboard is set up, and how
the US HHKB is set up today. But an ignored device gets **no** Karabiner rules, so the default
"right Command → Caps Lock" rule (`../docs/03-macos.md`) can't be used on it.

## Options

| | Option | LangToggle | Cost |
| --- | --- | --- | --- |
| **A** | Keep the US HHKB **ignored** by Karabiner. Live with a two-key toggle | **Ctrl+Space**, with "Select the previous input source" = Ctrl+Space (System Settings → Keyboard → Keyboard Shortcuts → Input Sources). Control is the key left of A | No remap at all, correct legends. Two keys instead of one. The Pro BT has no arrow cluster, so two-key combos are already normal on it, and this hurts less than it would on a JIS HHKB. Right ◇ stays Command |
| **B** | Set the virtual keyboard to **ANSI** | right Command → Caps Lock, as in `../docs/03-macos.md` | Breaks the JIS boards on the same Mac. Not an option for the author |
| **C** | Let Karabiner modify the US HHKB, and **undo every ANSI-vs-JIS difference** with rules scoped to it, with the virtual keyboard still JIS. Then remap right ◇ | right Command → Caps Lock (or Ctrl+Space) | A per-key translation table (about 20 keys, shifted and unshifted), tested in every input source. Apps that read raw key codes (games, VMs, remote desktop, some terminal modes) still see JIS codes |

**Don't** grab the US HHKB with Karabiner (option C) unless the translation rules are in place in
the same step. Without them it types JIS symbols.

Until option C is checked, the author's setup is **option A**: the HHKB stays `"ignore": true`,
and LangToggle is Ctrl+Space.

## Option C: the example rules (not yet tested)

[`../karabiner/hhkb-us-on-jis-virtual.json`](../karabiner/hhkb-us-on-jis-virtual.json) holds the
translation table above as Karabiner rules, scoped to the Pro BT (`vendor_id 1278`,
`product_id 514`). For another US board, change the `product_id`.

| Rule | Does |
| --- | --- |
| **type the US legends** [required] | The 18 rows of the table. Punctuation keys translate with Cmd or Ctrl held too, so Cmd+`[`, Cmd+Shift+`]`, Cmd+`=` etc. keep their character. The shifted number row translates only with Shift alone, so Cmd+Shift+3 / 4 / 5 keep their key codes |
| right Command → Caps Lock [default] | LangToggle, as in `../docs/03-macos.md` but scoped to product `514` |
| right Command → Ctrl+Space [alternative] | LangToggle, if Caps Lock switching misbehaves through the JIS virtual keyboard |

Use them in this order, so the HHKB never types JIS symbols in between:

1. Back up `karabiner.json`. Copy the file into `~/.config/karabiner/assets/complex_modifications/`.
2. Karabiner-Elements → Complex Modifications → Add predefined rule: enable **type the US legends**
   and **one** right Command rule. While the HHKB is still ignored, they do nothing.
3. Karabiner-Elements → Devices: turn on "Modify events" for `HHKB-BT`.
4. Type the test lines in every input source and compare them with the US legends. Check the
   shortcuts above.

Rolling back: turn "Modify events" off for the HHKB again (`"ignore": true`).

Not covered: characters typed with Option held, like `–` and `≠`, which come from JIS positions;
Option+`\` and Option+`=`; and apps that read raw key codes (games, VMs, remote desktop, some
terminal modes), which still see JIS codes. `TODO(hardware)`: test the rules, including the three
**inferred** rows and Caps Lock switching through the JIS virtual keyboard.
