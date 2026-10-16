# 04 — Windows

No example files. The agent writes the config from this page and
[`02-layout-spec.md`](02-layout-spec.md), and tells the user it's untested on a US board.
`TODO(hardware)`: check every step here on a real Windows machine. Findings marked "JIS repo"
were measured on Windows 11 25H2 with a JIS HHKB Studio in
[hhkb-ekj-trilingual](https://github.com/themrsung/hhkb-ekj-trilingual) (`docs/windows/`).

| Role | Windows | Comes from |
| --- | --- | --- |
| Aux | Alt | Hardware (stock Alt) |
| System | Win | Hardware (stock ◇) |
| LangToggle | Microsoft Korean IME: 한 ⇄ 영 | Keymap tool (`LANG1` on right ◇, native 한/영), else AutoHotkey v2 (right Win) |

On Windows, the two languages are **Korean IME 영 mode** (English letters, US layout) and
**Korean IME 한 mode**. On a US board both use the same US ANSI symbols, so the JIS repo's
"inconsistent symbols on Windows" issue doesn't apply here.

Every step below changes a system setting or installs something: 👤 ask first, and back up what
you change.

## Which path

| Board | Right ◇ from the keymap tool (`01-hardware.md`, Step 2) | AutoHotkey needed? |
| --- | --- | --- |
| HYBRID / Classic / Studio (Studio: on a Windows profile) | **`LANG1`** | **No.** Sections 1–3 only |
| **Pro BT**, Pro 2 (no keymap tool), or the tool step was skipped | stock (`RWin`) | **Yes.** Sections 1, 2 and 4 |

## 1. Survey (read-only)

```powershell
Get-WinUserLanguageList | ForEach-Object { "$($_.LanguageTag): $($_.InputMethodTips -join ', ')" }
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters' |
  Select-Object 'LayerDriver KOR', OverrideKeyboardIdentifier, OverrideKeyboardType, OverrideKeyboardSubtype
Get-PnpDevice -PresentOnly | Where-Object InstanceId -match 'VID_04FE'
Get-Command AutoHotkey64.exe -ErrorAction SilentlyContinue; Get-ScheduledTask -TaskName 'hhkb*' -ErrorAction SilentlyContinue
```

## 2. Input languages

Settings → Time & language → Language & region → Add a language → **Korean (한국어)** → keyboard
**Microsoft IME**. Or, after backing up the list:

```powershell
Get-WinUserLanguageList | ForEach-Object { "$($_.LanguageTag): $($_.InputMethodTips -join ', ')" } |
  Set-Content "$env:USERPROFILE\languagelist.bak-$(Get-Date -f yyyyMMddHHmmss).txt"
$list = Get-WinUserLanguageList
$list.Add('ko-KR')
Set-WinUserLanguageList $list -Force
```

**Windows keeps the input language per window** (JIS repo). 한/영 only works while the focused
window's language is Korean. In a window that's in English (United States), `LANG1` / `VK_HANGUL`
does nothing, and the key seems dead. See
[`../issues/windows-per-window-language.md`](../issues/windows-per-window-language.md). So:

- **Keymap-tool path (no AutoHotkey):** put **Korean first** in the language list, so new windows
  start in Korean. Removing the English (United States) keyboard entirely avoids the problem, but
  ask first: the user may want it. Win+Space recovers a window that's in English.
- **AutoHotkey path:** the script below pulls a window that's in another language into Korean, so
  the list order doesn't matter.

### Right Alt may become 한/영 (check this)

JIS repo measurement: a laptop's 한/영 key arrived as `VK_HANGUL` (`0x15`) on **scan code `138`**,
which is the **Right Alt** scan code. Windows' default Korean layout (`kbd101a.dll`, "Type 1") is
expected to turn Right Alt into 한/영 and Right Ctrl into 한자 while Korean is active.
`TODO(hardware)`: check on the HHKB. If the HHKB's right Alt toggles 한/영 instead of acting as
Alt:

- Tell the user, and ask. Some people like it (a second LangToggle on right Alt).
- To keep right Alt as Aux, change the Korean IME's hardware keyboard layout to one that doesn't
  remap Right Alt (Microsoft IME settings, or `LayerDriver KOR` in the registry; back up the value,
  admin, restart). `TODO(hardware)`: which option current Windows offers, and its exact name.

## 3. Keymap-tool path: `LANG1` on right ◇

Set right ◇ to **`LANG1`** in the keymap tool (`01-hardware.md`, Step 2). On the Studio, do it on
the profile the user picks for Windows, on every layer.

JIS repo: checked by the owner. A key set to **Mac かな** in the HHKB Studio Keymap Tool toggles
**한/영** in the Microsoft Korean IME, with no software at all. Mac かな sends HID `LANG1` (usage
`0x90`), the code a Korean USB keyboard's 한/영 key sends, and Windows turns it into `VK_HANGUL`.
`TODO(hardware)`: check that the US tools offer `LANG1`, and measure the codes (expected `VK 15 /
SC 0F2`).

This works only while Korean is the focused window's language (section 2). Check: section 5.

## 4. AutoHotkey path: right Win → LangToggle

AutoHotkey can't tell keyboards apart, so **this affects every keyboard's right Win key.** Tell
the user. (Per-device remapping needs a third-party kernel driver, AutoHotInterception; don't
install it unless the user asks.)

👤 Install AutoHotkey **v2** with the user's OK:

```powershell
winget install --id AutoHotkey.AutoHotkey -e
```

### `hhkb-us-bilingual.ahk`

Save as `%USERPROFILE%\Documents\AutoHotkey\hhkb-us-bilingual.ahk` (back up an existing file
first: `Copy-Item f "f.bak-$(Get-Date -f yyyyMMddHHmmss)"`). It's adapted from the JIS repo's
tested script (`docs/windows/03-autohotkey.md`), cut down to one key and one language.

```autohotkey
#Requires AutoHotkey v2.0
#SingleInstance Force
; hhkb-us-bilingual (Windows): right Win -> LangToggle in the Microsoft Korean IME (한 <-> 영).
; A window in another input language is first pulled into Korean, in 한 mode.

HKL_KO := 0x04120412, LANG_KO := 0x0412
CM_HANGUL := 0x01                          ; IME_CMODE_NATIVE = 한 in the Korean IME

HAN := {open: 1, cmode: CM_HANGUL}
ENG := {open: 0, cmode: 0}

RWin::LangToggle()

LangToggle() {
    if !(hwnd := FocusHwnd())
        return
    ; Decide the target once, from the current state, then set it (not toggle it), so the
    ; guarded retry below can never flip it back.
    t := (LangOf(hwnd) = LANG_KO && IsAt(hwnd, HAN)) ? ENG : HAN
    Apply(hwnd, t)
    SetTimer(() => (IsAt(hwnd, t) || Apply(hwnd, t)), -150)   ; verify once, retry only if needed
}

Apply(hwnd, t) {
    if LangOf(hwnd) != LANG_KO {
        DllCall("PostMessageW", "ptr", hwnd, "uint", 0x50, "ptr", 0, "ptr", HKL_KO)  ; WM_INPUTLANGCHANGEREQUEST
        deadline := A_TickCount + 150
        while LangOf(hwnd) != LANG_KO && A_TickCount < deadline
            Sleep(10)
    }
    ImeCtl(hwnd, 0x006, t.open)                 ; IMC_SETOPENSTATUS
    ImeCtl(hwnd, 0x002, t.cmode)                ; IMC_SETCONVERSIONMODE
}

IsAt(hwnd, t) {
    return LangOf(hwnd) = LANG_KO
        && ImeCtl(hwnd, 0x005) = t.open                        ; IMC_GETOPENSTATUS
        && (ImeCtl(hwnd, 0x001) & CM_HANGUL) = t.cmode         ; IMC_GETCONVERSIONMODE
}

; The window that has keyboard focus (a child control, if the app uses them).
FocusHwnd() {
    if !(active := WinExist("A"))
        return 0
    tid := DllCall("GetWindowThreadProcessId", "ptr", active, "ptr", 0, "uint")
    gti := Buffer(A_PtrSize = 8 ? 72 : 48, 0)   ; GUITHREADINFO
    NumPut("uint", gti.Size, gti, 0)
    if DllCall("GetGUIThreadInfo", "uint", tid, "ptr", gti)
        && (focus := NumGet(gti, A_PtrSize = 8 ? 16 : 12, "ptr"))
        return focus
    return active
}

LangOf(hwnd) {
    tid := DllCall("GetWindowThreadProcessId", "ptr", hwnd, "ptr", 0, "uint")
    return DllCall("GetKeyboardLayout", "uint", tid, "ptr") & 0xFFFF
}

ImeCtl(hwnd, cmd, value := 0) {
    if !(ime := DllCall("imm32\ImmGetDefaultIMEWnd", "ptr", hwnd, "ptr"))
        return -1
    result := 0
    DllCall("SendMessageTimeoutW", "ptr", ime, "uint", 0x283, "ptr", cmd, "ptr", value  ; WM_IME_CONTROL
          , "uint", 0x2, "uint", 200, "ptr*", &result)                                  ; SMTO_ABORTIFHUNG
    return result
}
```

Rules carried over from the JIS repo (don't "simplify" them away):

1. **The retry is guarded by a check**, and re-applies the same target. It never toggles twice.
2. **Wait for the language change** (up to 150 ms) before setting the IME state, or the IME
   command goes to the old language's IME.
3. `WM_IME_CONTROL` from another process is common practice but not formally documented.
   **Measure** that 한 / 영 actually change (section 5). `TODO(hardware)`: in particular, check
   that the Korean IME reports 영 as open status 0 / conversion mode 0, as assumed here.

**Minimal fallback**, if the IME-control calls don't work on the user's Windows build, and Korean
is the only (or first) language: `RWin::Send("{vk15sc138}")` (sends 한/영, the code the JIS repo
measured from a laptop's 한/영 key; `TODO(hardware)`: not tested as a send). It only works while
the window is already in Korean.

### Start it at sign-in: a Task Scheduler logon task

**Don't use `shell:startup` or a Run key.** JIS repo: on the owner's PC, neither started AutoHotkey
at sign-in over three restarts (Windows skipped the entry without logging anything). A logon
task did. Creating it needs admin (one UAC prompt), but the task runs as the user, **not**
elevated:

```powershell
# Run in an elevated PowerShell. $user must be the account that signs in (DOMAIN\name).
$user   = "$env:USERDOMAIN\$env:USERNAME"
$script = "$env:USERPROFILE\Documents\AutoHotkey\hhkb-us-bilingual.ahk"
$a = New-ScheduledTaskAction -Execute 'C:\Program Files\AutoHotkey\v2\AutoHotkey64.exe' -Argument "`"$script`""
$t = New-ScheduledTaskTrigger -AtLogOn -User $user
$p = New-ScheduledTaskPrincipal -UserId $user -LogonType Interactive -RunLevel Limited
$s = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries `
       -ExecutionTimeLimit ([TimeSpan]::Zero) -MultipleInstances IgnoreNew
Register-ScheduledTask -TaskName 'hhkb-us-bilingual' -Action $a -Trigger $t -Principal $p -Settings $s -Force
```

The settings matter: by default a task doesn't start on battery and is stopped after 3 days.
Start it now with `Start-ScheduledTask hhkb-us-bilingual`; `LastTaskResult` `267009` (`0x41301`)
means it's running. Remove it with `Unregister-ScheduledTask hhkb-us-bilingual`. After a restart,
check that the green **H** tray icon is there.

**Elevated (admin) windows:** a non-elevated AutoHotkey can't send messages to them (UIPI), so
right Win won't switch there. Known limitation; the keymap-tool path doesn't have it.

**Bluetooth:** AutoHotkey acts on the key, not the device, so the same script works over USB and
Bluetooth (JIS repo: checked on a JIS Studio over Bluetooth LE).

## 5. Check

- AutoHotkey path: tray icon → Open → View → **Key history**. Pressing right ◇ should show `RWin`
  coming in. Keymap-tool path: run a two-line script (`InstallKeybdHook` then `KeyHistory`),
  press right ◇, and press F5 in the window: expect `VK 15`.
- Press right Alt the same way: it should be `A5` (RAlt), not `15` (see section 2).
- The language indicator in the taskbar should switch between `A` and `가` on each tap.

Don't use a registry `Scancode Map` unless the user asks. It needs a reboot, can't be scoped to
the HHKB, and is easy to leave behind. PowerToys Keyboard Manager can remap right Win to a key,
but can't pull a window into Korean first, so it isn't one of this repo's paths.
