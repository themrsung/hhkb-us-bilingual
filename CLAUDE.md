# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

The end-user setup instructions (what an agent does on someone's machine) live in AGENTS.md:

@AGENTS.md

## Working on this repo (maintainer notes)

This repo has no build, tests or code. It's Markdown instructions plus example Karabiner-Elements
rule files. It's the US-layout sibling of `hhkb-ekj-trilingual` (JIS), and copies that repo's
structure and conventions.

**Status: planning.** The author's board is an **HHKB Professional BT** (US, PD-KB600B), used
over Bluetooth with a MacBook Air (M4, US built-in keyboard) whose Karabiner virtual keyboard is JIS
(USB for power; check whether it carries data). DIP switches: SW1 OFF, SW2 ON (Mac mode), SW3–SW5
OFF, SW6 ON (Wake Up). It has no keymap-tool
support, so the author's own path is DIP switches → software. The HYBRID / Studio notes are for
other users, who use PFU's keymap tool only to set the bottom row. Every unverified fact is marked
`TODO(hardware)`; findings copied from the JIS repo say "JIS repo". When the hardware arrives,
work through them and remove each marker once it's checked:

```sh
grep -rn "TODO(hardware)" --include='*.md' .
```

Check that the example rules are valid JSON after any edit:

```sh
for f in karabiner/*.json; do python3 -m json.tool "$f" >/dev/null && echo "ok $f"; done
```

### How the docs fit together

- `docs/02-layout-spec.md` is the source of truth. It defines the roles (Aux / System /
  LangToggle) and the per-OS mapping table. `docs/03`–`05` and `karabiner/*.json` translate it
  for each OS. When the layout changes, update the spec first, then every translation, then the
  bottom-row tables in `docs/01-hardware.md` and `docs/07-verification.md`.
- The rule descriptions and default/alternative split in `karabiner/hhkb-us-bilingual.json` must
  match the table in `docs/03-macos.md`. `karabiner/hhkb-us-on-jis-virtual.json` must match the
  measured table and rule table in `issues/macos-jis-virtual-keyboard.md`.
- The hardware ladder (DIP switches → keymap tool → software) is stated in `AGENTS.md` section 2,
  `docs/01-hardware.md` and `docs/02-layout-spec.md`. Keep the three in step.
- No binaries or scripts. Only macOS gets example config files. Windows gets one inline AutoHotkey
  script in `docs/04-windows.md`; other OSes get prose plus short inline snippets that the setup
  agent adapts.
- `issues/` holds known issues carried over from the JIS repo. Each file says what was seen there
  and has a `TODO(hardware)` until it's checked on a US board. List new ones in `issues/README.md`
  and in AGENTS.md's file table.
- README has **three** interactive prompts (macOS / Windows / Advanced). They share the same
  source-of-truth block, questions and closing paragraph; change all three together. They and the
  doc list must match AGENTS.md's file table and order of operations.

### Versioning (same as hhkb-ekj-trilingual)

- Semver, released as **annotated git tags** `vX.Y.Z`. Each update gets a new tag, so prompts pinned
  to older tags keep working.
- The README setup prompt is **pinned to the current release tag**. On release, bump every
  occurrence of the tag in `README.md` (the "pinned to" sentence, and in each of the three prompts
  the first line and the `blob/`, `--branch` and `raw.githubusercontent.com` URLs) in the same
  commit: `grep -n 'v[0-9]' README.md`.
- Release commits put the version at the end of the subject line, e.g.
  `Document the Studio US DIP switches (v1.0.1)`, followed by `git tag -a vX.Y.Z -m "vX.Y.Z: <summary>"`.
- Releases not yet tested on hardware get a `-dev` suffix (`v0.1.0-dev`). Drop the suffix only
  once the release has been checked on a real US board. `v1.0.0` = first fully hardware-tested
  release.
