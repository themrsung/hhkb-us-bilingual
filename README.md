# hhkb-us-bilingual

> [!WARNING]
> **This entire repo was AI-generated**: the docs, the example Karabiner rules, and this README.
> If you don't want AI-written config on your machine, **skip the interactive setup** and read
> the files yourself before applying anything.

> [!IMPORTANT]
> **Status: planning (v0.x).** Not yet tested on real hardware. Every fact still waiting for a
> hardware check is marked `TODO(hardware)` in the docs. The first release tested on a real board
> will be `v1.0.0`.

> **Have a JIS HHKB?** Use [**hhkb-ekj-trilingual**](https://github.com/themrsung/hhkb-ekj-trilingual)
> instead. It covers English / Korean / Japanese on the JIS HHKB Professional HYBRID Type-S and
> the JIS HHKB Studio, using the JIS-only 英数 / かな keys. This repo is for the **HHKB US (ANSI)
> layout**.

Two languages (by default English and Korean) on an **HHKB US-layout** board, switched with one
thumb key, on **any OS**.

```
Aux | System | Space | LangToggle | Aux
```

| Role | What it is | macOS | Windows | Linux | Android | iPadOS |
| --- | --- | --- | --- | --- | --- | --- |
| **System** | The OS's main modifier | Command | Win | Super | Meta | Command |
| **Aux** | The OS's second modifier | Option | Alt | Alt | Alt | Option |
| **LangToggle** | Switches between the two languages | Caps Lock input switching | 한/영 (Hangul) | IME toggle (e.g. Hangul) | Ctrl+Space | Caps Lock / Globe |

**Hardware first, software last.** Set the DIP switches, then (on boards PFU's keymap tool
supports) use the keymap tool, and only then remap in software. On a stock HHKB US the bottom row
already is `Alt/Option | ◇ | Space | ◇ | Alt/Option`, and ◇ is the System key on every OS, so
**only the right ◇ changes**: to LangToggle, in the keymap tool if the model has one, otherwise in
software. Boards without a keymap tool, like the HHKB Professional BT, always use software for it.
You can change the layout to taste; see [`docs/06-customization.md`](docs/06-customization.md).

## How this repo differs from the trilingual one

- **No executable binaries, no scripts to run.** The repo is Markdown instructions for an AI agent
  (or a human), plus **example drag-and-drop Karabiner-Elements rule files** for macOS.
- **OS-agnostic.** [`docs/02-layout-spec.md`](docs/02-layout-spec.md) describes the layout as
  roles, not key codes. On Windows, Linux, Android, etc., the agent turns that spec into the native
  tool's config (AutoHotkey on Windows, keyd on Linux, and so on). Only macOS has
  ready-made example files.

## Scope

- **HHKB US (ANSI) layout only.** Target models: HHKB Professional BT, HYBRID Type-S / HYBRID /
  Classic US, and HHKB Studio US. The author's board is a **Professional BT** (Bluetooth), so
  that's the one that will be tested. The HYBRID and Studio notes come from PFU's manuals and the
  JIS repo's findings.
- **macOS** has example config files. **Windows** gets a ready-to-adapt AutoHotkey v2 script in
  the docs. **Other OSes** get instructions only. The agent builds the config from the spec and
  tells you it's untested there.
- The defaults (English ABC ⇄ Korean 2-Set) are the author's own preferences. You can choose any
  other two input sources.

## Interactive setup (for a coding agent)

Pick the prompt for the machine you're setting up, and paste it into a coding agent running on
that machine (Claude Code, Codex, etc.). Each one asks you a few questions, then does the rest on
its own. It stops only for steps that need your hands: DIP switches, the keymap tool, Bluetooth
pairing, permission prompts, and key-press checks.

| Prompt | For | Software it uses |
| --- | --- | --- |
| [macOS](#macos) | A Mac | Karabiner-Elements (example rules in `karabiner/`), only if the keyboard can't do LangToggle itself |
| [Windows](#windows) | A Windows 11 PC | AutoHotkey v2 (script in `docs/04-windows.md`), only if the keyboard can't do LangToggle itself |
| [Advanced](#advanced) | Any OS, your own remapper or firmware, or custom firmware | Whatever you already use, or a firmware build you approve |

All three are **pinned to the release tag `v0.2.0-dev`**. The agent reads the docs exactly as they
were at that release, even if `main` changes later.

### macOS

```text
Set up my HHKB US-layout bilingual keyboard on this Mac, following the hhkb-us-bilingual repo
pinned at tag v0.2.0-dev. This is the macOS path: Karabiner-Elements is the software layer.

Source of truth (use this tag only — not main, not any other ref):
  Entry point: https://github.com/themrsung/hhkb-us-bilingual/blob/v0.2.0-dev/AGENTS.md
  Get the repo:  git clone --depth 1 --branch v0.2.0-dev https://github.com/themrsung/hhkb-us-bilingual.git
  (If you can't clone, fetch each file from
   https://raw.githubusercontent.com/themrsung/hhkb-us-bilingual/v0.2.0-dev/<path>, starting with
   AGENTS.md and then every file under docs/, issues/ and karabiner/ that it links to.)
Clone into a temporary/scratch directory, read AGENTS.md, all of docs/ and issues/ in full, and
follow them. Do not download or run any binaries; this repo contains none.

Before changing anything:
1. Check the OS. If this isn't macOS, stop and tell me to use the Windows or Advanced prompt
   from the README instead.
2. Ask me, in one batch, and show the default for each:
   a. Which HHKB I have, and how it connects (USB, Bluetooth, or both). The repo is for the US
      (ANSI) layout. If I have a JIS board, point me to
      https://github.com/themrsung/hhkb-ekj-trilingual and ask whether to continue anyway.
   b. The two languages LangToggle switches between (default: English ⇄ Korean 2-Set).
   c. The bottom row (default: Aux | System | Space | LangToggle | Aux).
   d. Which other OSes this keyboard is used with. It decides what the keymap tool can do.
   If I just say "defaults", use the repo defaults for everything.
3. Look at what is already installed and configured (Karabiner-Elements and its karabiner.json,
   other remappers, enabled input sources, the keyboard type macOS has for the HHKB) and tell me
   what you'll add or change. Back up any file before editing it. Merge, never overwrite my
   other rules.

Work down the ladder in AGENTS.md and don't skip a rung:
1. DIP switches first (docs/01-hardware.md). Tell me exactly which switches to set, wait for me,
   then check the raw bottom-row codes in a key-event viewer over every connection I use.
2. Then the keymap tool, if my model has one (the Pro BT and Pro 2 don't). Tell me exactly what to
   set on the bottom row (on the HHKB Studio: on every layer, and on the profile for this OS), and
   wait for me to write it.
3. Only then software, for whatever the keyboard couldn't do.
   On macOS that means: set up the input sources and Caps Lock switching (docs/03-macos.md), and
   install Karabiner-Elements and enable one rule from karabiner/hhkb-us-bilingual.json only if
   the right ◇ still needs remapping. Ask before installing anything. If the keyboard is used
   over Bluetooth, check that Karabiner modifies its Bluetooth device entry too.

Pause only for things I must do by hand (DIP switches, the keymap tool, Bluetooth pairing,
approving system extensions and permissions, and pressing keys for the checks). Give me exact
click-by-click instructions for those and wait for me to confirm. Wherever a doc says
TODO(hardware) or "JIS repo", check it on my machine instead of trusting it, and tell me what you
found. Finish by going through the checklist in docs/07-verification.md with me, then summarize
what changed and where the backups are.
```

### Windows

```text
Set up my HHKB US-layout bilingual keyboard on this Windows PC, following the hhkb-us-bilingual
repo pinned at tag v0.2.0-dev. This is the Windows path: AutoHotkey v2 is the software layer.

Source of truth (use this tag only — not main, not any other ref):
  Entry point: https://github.com/themrsung/hhkb-us-bilingual/blob/v0.2.0-dev/AGENTS.md
  Get the repo:  git clone --depth 1 --branch v0.2.0-dev https://github.com/themrsung/hhkb-us-bilingual.git
  (If you can't clone, fetch each file from
   https://raw.githubusercontent.com/themrsung/hhkb-us-bilingual/v0.2.0-dev/<path>, starting with
   AGENTS.md and then every file under docs/, issues/ and karabiner/ that it links to.)
Clone into a temporary/scratch directory, read AGENTS.md, all of docs/ and issues/ in full, and
follow them. Do not download or run any binaries; this repo contains none.

Before changing anything:
1. Check the OS. If this isn't Windows, stop and tell me to use the macOS or Advanced prompt
   from the README instead.
2. Ask me, in one batch, and show the default for each:
   a. Which HHKB I have, and how it connects (USB, Bluetooth, or both). The repo is for the US
      (ANSI) layout. If I have a JIS board, point me to
      https://github.com/themrsung/hhkb-ekj-trilingual and ask whether to continue anyway.
   b. The two languages LangToggle switches between (default: English ⇄ Korean 2-Set).
   c. The bottom row (default: Aux | System | Space | LangToggle | Aux).
   d. Which other OSes this keyboard is used with. It decides what the keymap tool can do.
   If I just say "defaults", use the repo defaults for everything.
3. Run the read-only survey in docs/04-windows.md (input languages, Korean layout, the HHKB's
   device entry, any existing AutoHotkey install or scheduled task) and tell me what you'll add
   or change. Back up anything before editing it (language list, registry values, scripts).
   Merge, never overwrite my other scripts.

Work down the ladder in AGENTS.md and don't skip a rung:
1. DIP switches first (docs/01-hardware.md). Tell me exactly which switches to set, wait for me,
   then check the raw bottom-row codes in a key-event viewer over every connection I use.
2. Then the keymap tool, if my model has one (the Pro BT and Pro 2 don't). Tell me exactly what to
   set on the bottom row (on the HHKB Studio: on every layer, and on the profile for this OS), and
   wait for me to write it.
3. Only then software, for whatever the keyboard couldn't do.
   On Windows that means: add the Microsoft Korean IME (docs/04-windows.md), and install
   AutoHotkey v2, write the script from docs/04-windows.md, and start it with a Task Scheduler
   logon task (not shell:startup or a Run key) only if the right ◇ still needs remapping. Ask
   before installing anything and before every system-setting change. Check whether the right
   Alt key turns into 한/영 with the Korean IME, and ask me what to do if it does.

Pause only for things I must do by hand (DIP switches, the keymap tool, Bluetooth pairing,
approving system extensions and permissions, and pressing keys for the checks). Give me exact
click-by-click instructions for those and wait for me to confirm. Wherever a doc says
TODO(hardware) or "JIS repo", check it on my machine instead of trusting it, and tell me what you
found. Finish by going through the checklist in docs/07-verification.md with me, then summarize
what changed and where the backups are.
```

### Advanced

```text
Help me set up my HHKB US-layout bilingual keyboard, following the hhkb-us-bilingual repo pinned
at tag v0.2.0-dev. This is the Advanced path (docs/08-advanced.md): fit the layout into a setup I
already have, or help me build custom firmware for my keyboard.

Source of truth (use this tag only — not main, not any other ref):
  Entry point: https://github.com/themrsung/hhkb-us-bilingual/blob/v0.2.0-dev/AGENTS.md
  Get the repo:  git clone --depth 1 --branch v0.2.0-dev https://github.com/themrsung/hhkb-us-bilingual.git
  (If you can't clone, fetch each file from
   https://raw.githubusercontent.com/themrsung/hhkb-us-bilingual/v0.2.0-dev/<path>, starting with
   AGENTS.md and then every file under docs/, issues/ and karabiner/ that it links to.)
Clone into a temporary/scratch directory, read AGENTS.md, all of docs/ and issues/ in full, and
follow them. Do not download or run any binaries; this repo contains none.

Before changing anything:
1. Check the OS, and tell me that nothing on this path is tested.
2. Ask me, in one batch, and show the default for each:
   a. Which path: (A) fit the layout into my existing setup — my OS, remapper or firmware
      (kanata, keyd, Hammerspoon, QMK/VIA, an existing Karabiner or AutoHotkey config, …) — or
      (B) custom firmware for my specific keyboard. Default: A.
   b. Which HHKB I have, and how it connects (USB, Bluetooth, or both). The repo is for the US
      (ANSI) layout. If I have a JIS board, point me to
      https://github.com/themrsung/hhkb-ekj-trilingual and ask whether to continue anyway.
   c. The two languages LangToggle switches between (default: English ⇄ Korean 2-Set).
   d. The bottom row (default: Aux | System | Space | LangToggle | Aux).
   e. Which other OSes this keyboard is used with. It decides what the keymap tool can do.
   If I just say "defaults", use the repo defaults for everything.
   Then ask me to point you at (or paste) my existing config files and firmware keymap, and
   read them before proposing anything.
3. Tell me what you'll add or change. Back up any file before editing it. Merge, never
   overwrite my other rules.

Work down the ladder in AGENTS.md and don't skip a rung:
1. DIP switches first (docs/01-hardware.md). Tell me exactly which switches to set, wait for me,
   then check the raw bottom-row codes in a key-event viewer over every connection I use.
2. Then the keymap tool, if my model has one (the Pro BT and Pro 2 don't). Tell me exactly what to
   set on the bottom row (on the HHKB Studio: on every layer, and on the profile for this OS), and
   wait for me to write it.
3. Only then software, for whatever the keyboard couldn't do.
   Path A: translate docs/02-layout-spec.md into my tool, in its own style, show me the diff,
   then apply it. Path B: first tell me plainly what custom firmware involves for my model
   (usually a replacement controller: opening the case, the warranty, which stock features may
   not carry over), and ask whether to go on. Then research what fits my exact model, with
   sources, and let me choose. Write the keymap and build it with that firmware's own toolchain
   only after I agree to install it. Keep the source and build output in my own folder or fork,
   never in this repo. Never buy anything, and never flash without me.

Pause only for things I must do by hand (DIP switches, the keymap tool, Bluetooth pairing,
approving system extensions and permissions, flashing firmware, and pressing keys for the checks). Give me exact
click-by-click instructions for those and wait for me to confirm. Wherever a doc says
TODO(hardware) or "JIS repo", check it on my machine instead of trusting it, and tell me what you
found. Finish by going through the checklist in docs/07-verification.md with me, then summarize
what changed and where the backups are.
```

To set it up by hand instead, follow [`AGENTS.md`](AGENTS.md), then `docs/01` → `docs/07` in
order (skipping the OS docs that don't apply to you), with [`docs/08-advanced.md`](docs/08-advanced.md)
for the Advanced path. Known issues are in [`issues/`](issues/README.md).

## Maintenance

**This repo will not be serviced.** No issues, no support, no guarantees. Fork it and use it as
you see fit.

The author may update it from time to time, but only to match their own preferences, and only
tested on the HHKB models and OSes they own. Each update gets a new tag, so older prompts pinned to
an earlier tag keep working as they were.

## License

[CC0 1.0 Universal](LICENSE). Public domain dedication: no rights reserved, no attribution
required.
