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

**Set the DIP switches to get as close to this row as possible, so you need few or no software
rebinds.** On a stock HHKB US the bottom row already is `Alt/Option | ◇ | Space | ◇ | Alt/Option`,
and ◇ is the System key on every OS. So the plan is: **only right ◇ → LangToggle is remapped in
software.** You can change the layout to taste; see [`docs/06-customization.md`](docs/06-customization.md).

## How this repo differs from the trilingual one

- **No executable binaries, no scripts to run.** The repo is Markdown instructions for an AI agent
  (or a human), plus **example drag-and-drop Karabiner-Elements rule files** for macOS.
- **OS-agnostic.** [`docs/02-layout-spec.md`](docs/02-layout-spec.md) describes the layout as
  roles, not key codes. On Windows, Linux, Android, etc., the agent turns that spec into the native
  tool's config (AutoHotkey or PowerToys on Windows, keyd on Linux, and so on). Only macOS has
  ready-made example files.

## Scope

- **HHKB US (ANSI) layout only.** Target models: HHKB Professional HYBRID Type-S / HYBRID /
  Classic US and HHKB Studio US. `TODO(hardware)`: only the model the author ends up owning will be
  tested.
- **macOS** has example config files. **Other OSes** get instructions only. The agent builds
  the config from the spec and tells you it's untested there.
- The defaults (English ABC ⇄ Korean 2-Set) are the author's own preferences. You can choose any
  other two input sources.

## Interactive setup (for a coding agent)

Paste the prompt below into a coding agent running on the machine you want to set up (Claude
Code, Codex, etc.). It asks you a few questions, then does the rest on its own. It stops only for
steps that need your hands: DIP switches, the keymap tool, permission prompts, and key-press
checks.

The prompt is **pinned to the release tag `v0.1.0-dev`**. The agent reads the docs exactly as they
were at that release, even if `main` changes later.

```text
Set up my HHKB US-layout bilingual keyboard configuration on this machine, following the
hhkb-us-bilingual repo pinned at tag v0.1.0-dev.

Source of truth (use this tag only — not main, not any other ref):
  Entry point: https://github.com/themrsung/hhkb-us-bilingual/blob/v0.1.0-dev/AGENTS.md
  Get the repo:  git clone --depth 1 --branch v0.1.0-dev https://github.com/themrsung/hhkb-us-bilingual.git
  (If you can't clone, fetch each file from
   https://raw.githubusercontent.com/themrsung/hhkb-us-bilingual/v0.1.0-dev/<path>, starting with
   AGENTS.md and then every file under docs/ and karabiner/ that it links to.)
Clone into a temporary/scratch directory, read AGENTS.md and all of docs/ in full, and follow them.
Do not download or run any binaries; this repo contains none.

Before changing anything:
1. Check the OS. macOS has ready-made example Karabiner-Elements rules. For any other OS, tell me
   there are no example files and the result is untested, then build the config for that OS's
   native remapping tool from docs/02-layout-spec.md. Name the tool you plan to use and ask
   before installing it.
2. Ask me, in one batch, and show the default for each:
   a. Which HHKB I have. The repo is for the US (ANSI) layout. If I have a JIS board, point me
      to https://github.com/themrsung/hhkb-ekj-trilingual and ask whether to continue anyway.
   b. The two languages LangToggle switches between (default: English ABC ⇄ Korean 2-Set).
   c. The bottom row (default: Aux | System | Space | LangToggle | Aux).
   d. How LangToggle is sent on this OS (default: the one docs/02-layout-spec.md gives).
   If I just say "defaults", use the repo defaults for everything.
3. Look at what is already installed and configured (remapping tools, their config files,
   enabled input sources / IMEs) and tell me what you'll add or change. Back up any file before
   editing it. Merge, never overwrite my other rules.

Then do the setup on your own in the order AGENTS.md gives. Pause only for things I must do
by hand (DIP switches — which must be set BEFORE any keymap tool keymap — the keymap tool
itself, approving system extensions and permissions, and pressing keys for the checks). Give me
exact click-by-click instructions for those and wait for me to confirm. Finish by going through
the checklist in docs/07-verification.md with me, then summarize what changed and where the
backups are.
```

To set it up by hand instead, follow [`AGENTS.md`](AGENTS.md), then `docs/01` → `docs/07` in
order (skipping the OS docs that don't apply to you).

## Maintenance

**This repo will not be serviced.** No issues, no support, no guarantees. Fork it and use it as
you see fit.

The author may update it from time to time, but only to match their own preferences, and only
tested on the HHKB models and OSes they own. Each update gets a new tag, so older prompts pinned to
an earlier tag keep working as they were.

## License

[CC0 1.0 Universal](LICENSE). Public domain dedication: no rights reserved, no attribution
required.
