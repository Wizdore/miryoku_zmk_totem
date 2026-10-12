# Shaon's ToTeM

Personal ZMK firmware for my **TOTEM** split keyboard, using the
[Miryoku](https://github.com/manna-harbour/miryoku) layout. This README documents
*this exact setup*: every layer, shortcut, setting and procedure. If something here
disagrees with the files in this repo, the files win.

Keys are named by their **Base-layer letter** (Colemak-DH) throughout, e.g. "the O key"
means the right pinky home-row key, whatever layer you are on.

## Contents

1. [At a glance](#at-a-glance)
2. [Cheat sheet](#cheat-sheet)
3. [Layers](#layers)
4. [How keys behave](#how-keys-behave)
5. [Bluetooth, USB and pairing](#bluetooth-usb-and-pairing)
6. [Battery and sleep](#battery-and-sleep)
7. [ZMK Studio (live keymap editing)](#zmk-studio-live-keymap-editing)
8. [Getting and flashing firmware](#getting-and-flashing-firmware)
9. [Changing the configuration](#changing-the-configuration)
10. [Troubleshooting](#troubleshooting)
11. [Repository layout](#repository-layout)
12. [Credits](#credits)

## At a glance

| | |
|---|---|
| Keyboard | [TOTEM](https://github.com/GEIGEIGEIST/TOTEM) by GEIGEIGEIST: 38-key wireless split (3×5 per side, one extra outer pinky key per side, 3 thumb keys per side) |
| Controllers | 2 × Seeed XIAO nRF52840 (BLE). ZMK board name `seeeduino_xiao_ble` |
| Halves | **Left = central**: talks to the computer (USB or Bluetooth), runs ZMK Studio, reports both batteries. **Right = peripheral**: talks only to the left half |
| Firmware | [ZMK](https://zmk.dev) **v0.3** (pinned; Zephyr 3.5) |
| Layout | Miryoku, **Colemak-DH** alphas, **vi-style** arrows, **macOS** (⌘) clipboard shortcuts, Back/Forward mouse buttons added |
| Device name | `Shaon's ToTeM` (Bluetooth and USB). USB ID `1D50:615E` (ZMK default) |
| Bluetooth | 5 host profiles (keys for profiles 0–3), plus the link between the halves |
| Sleep | Deep sleep after **4 hours** without key presses |
| Battery | Both halves reported to the computer; shown with ZMK Battery Bar on macOS |
| Live editing | ZMK Studio on the left half, over USB or Bluetooth, no unlock needed |
| Builds | GitHub Actions on every push; `master` builds are published to the [`latest` release](../../releases/latest) |

## Cheat sheet

| I want to… | Do this |
|---|---|
| Type one capital letter | Hold the opposite hand's home-row Shift (**T** for right-hand letters, **N** for left-hand letters) for about 200 ms, then tap the letter |
| Type a WHOLE WORD in capitals | **Caps Word**: hold **Space**, tap **O**. Type the word; numbers and `_` keep it on, Space or any punctuation (including `-`) ends it |
| Toggle Caps Lock | Hold **Space**, hold **T** (Shift on this layer), tap **O** |
| Hold Backspace / Delete to repeat | Hold **Space**, then hold **Bksp** / **Del** (the plain copies on the Nav layer auto-repeat) |
| Arrows, Home/End, Page Up/Down | Hold **Space**: M N E I = ← ↓ ↑ →, K H , . = Home PgDn PgUp End |
| Copy / Cut / Paste / Undo / Redo | Hold **Space** or **Tab**, right top row: J=Redo L=Paste U=Copy Y=Cut '=Undo. Or hold **Z** or **/** (Button layer) |
| Numbers | Hold **Bksp**: left hand becomes a numpad (7 8 9 / 4 5 6 / 1 2 3, 0 on the Space thumb) |
| Symbols | Hold **Enter** |
| F1–F12 | Hold **Del** |
| Mouse | Hold **Tab**: M N E I move, K H , . scroll, right thumbs click (Enter=right, Bksp=left, Del=middle) |
| Mouse Back / Forward | Hold **Tab**, tap **O** (Back) or **/** (Forward) |
| Volume, play/pause, track | Hold **Esc**: N/E = Vol−/Vol+, M/I = Prev/Next, right thumbs Stop / Play-Pause / Mute |
| Switch Bluetooth device | Hold **Esc**, tap **K / H / , / .** for profile 0 / 1 / 2 / 3 |
| Forget a Bluetooth pairing | Hold **Esc**, hold **T** (Shift), tap that profile's key. Clears only that profile |
| Switch output USB ↔ Bluetooth | Hold **Esc**, tap **/**. With **T** (Shift) held: force USB |
| Lock a layer on (no holding) | Hold its thumb key, **double-tap D** (left-thumb layers: Nav, Mouse, Media) or **double-tap H** (right-thumb layers: Num, Sym, Fun). Example: Nav lock = hold Space, double-tap D |
| Back to normal from a locked layer | **Double-tap P** (left-hand layers) or **double-tap L** (right-hand layers) |
| Enter bootloader (to flash) | **Left half:** hold Space, tap **Q**. **Right half:** hold Enter, tap **'**. Or double-press the half's reset button |
| Edit the keymap live | ZMK Studio, see below |

## Layers

Ten layers, numbered in firmware order. Hold a thumb key (or Z or /) to use a layer
while held; release to return to Base.

| # | Layer | Reach it by holding | Contents |
|---|---|---|---|
| 0 | Base | (default) | Colemak-DH letters, home-row mods, thumb layer keys |
| 1 | Extra | hold any thumb, double-tap F (left thumb) or U (right thumb) | Alternative Base: QWERTY with the same mods and thumbs |
| 2 | Tap | hold any thumb, double-tap W (left thumb) or Y (right thumb) | Base with no hold functions at all (for games etc.) |
| 3 | Button | Z or / (bottom pinky) | Mouse buttons, clipboard, mods, for both hands |
| 4 | Nav | Space (left middle thumb) | Arrows, editing, clipboard, Caps Word on the right hand |
| 5 | Mouse | Tab (left inner thumb) | Mouse movement, scroll, buttons on the right hand |
| 6 | Media | Esc (left outer thumb) | Volume, media, Bluetooth on the right hand |
| 7 | Num | Bksp (right middle thumb) | Numpad on the left hand |
| 8 | Sym | Enter (right inner thumb) | Symbols on the left hand |
| 9 | Fun | Del (right outer thumb) | F-keys on the left hand |

**How the diagrams read**

- Top line = what a tap does. Bottom line = what holding does (Base/Extra), or a note.
- `2x lock` = **double-tap** (within 200 ms) to switch to that layer and stay there.
- `S:…` = what the key does while Shift is held.
- Blank = does nothing.
- `Cmd` = ⌘ Command (the Windows key on Windows), `Opt` = ⌥ Option (Alt), `ROpt` =
  right Option (AltGr on Windows/Linux), `Ctrl`, `Shift`.
- `(rgb)` / `(ext)` = Miryoku's RGB-light and external-power keys. They do nothing here:
  this TOTEM has no RGB LEDs and no switchable power rail.
- The extra **outer bottom pinky keys** always repeat the top-row pinky key of the same
  side (Q / ' on Base).
- On every layer you hold with a thumb, the **opposite hand** has the layer's keys and the
  **same hand** has plain Cmd/Opt/Ctrl/Shift (bottom row: ROpt), so you can hold mods
  and the layer together with one hand.

### 0 Base

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   Q   |   W   |   F   |   P   |   B   |  |   J   |   L   |   U   |   Y   |   '   |
        |       |       |       |       |       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   A   |   R   |   S   |   T   |   G   |  |   M   |   N   |   E   |   I   |   O   |
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   Q   |   Z   |   X   |   C   |   D   |   V   |  |   K   |   H   |   ,   |   .   |   /   |   '   |
|       | Button|  ROpt |       |       |       |  |       |       |       |  ROpt | Button|       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |  Esc  | Space |  Tab  |  | Enter |  Bksp |  Del  |
                        | Media |  Nav  | Mouse |  |  Sym  |  Num  |  Fun  |
                        +-------+-------+-------+  +-------+-------+-------+
```

Home-row mods: A=Cmd, R=Opt, S=Ctrl, T=Shift and mirrored N=Shift, E=Ctrl, I=Opt, O=Cmd.
X and . are right Option when held. Z and / open the Button layer when held.

### 1 Extra

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   Q   |   W   |   E   |   R   |   T   |  |   Y   |   U   |   I   |   O   |   P   |
        |       |       |       |       |       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   A   |   S   |   D   |   F   |   G   |  |   H   |   J   |   K   |   L   |   '   |
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   Q   |   Z   |   X   |   C   |   V   |   B   |  |   N   |   M   |   ,   |   .   |   /   |   P   |
|       | Button|  ROpt |       |       |       |  |       |       |       |  ROpt | Button|       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |  Esc  | Space |  Tab  |  | Enter |  Bksp |  Del  |
                        | Media |  Nav  | Mouse |  |  Sym  |  Num  |  Fun  |
                        +-------+-------+-------+  +-------+-------+-------+
```

QWERTY with the same mods and thumb keys. Switch to it with a left thumb held +
double-tap **F** (or a right thumb + **U**); return with a left thumb held + double-tap
**P** (or a right thumb + **L**).

### 2 Tap

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   Q   |   W   |   F   |   P   |   B   |  |   J   |   L   |   U   |   Y   |   '   |
        |       |       |       |       |       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   A   |   R   |   S   |   T   |   G   |  |   M   |   N   |   E   |   I   |   O   |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   Q   |   Z   |   X   |   C   |   D   |   V   |  |   K   |   H   |   ,   |   .   |   /   |   '   |
|       |       |       |       |       |       |  |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |  Esc  | Space |  Tab  |  | Enter |  Bksp |  Del  |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Letters and thumbs only, no hold functions and no layer keys. Entered with a left thumb
held + double-tap **W** (or a right thumb + **Y**). **The only way out is restarting the
left half**: press its reset button once (or power it off and on).

### 3 Button

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Undo |  Cut  |  Copy | Paste |  Redo |  |  Redo | Paste |  Copy |  Cut  |  Undo |
        |       |       |       |       |       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|  Undo |  Undo |  Cut  |  Copy | Paste |  Redo |  |  Redo | Paste |  Copy |  Cut  |  Undo |  Undo |
|       |       |       |       |       |       |  |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        | Click | Click | Click |  | Click | Click | Click |
                        | Middle|  Left | Right |  | Right |  Left | Middle|
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Z** or **/**. Mouse clicks on all thumbs, clipboard on top and bottom rows,
mods on the home row, all mirrored for either hand. Handy with a real mouse/trackpad.

### 4 Nav

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Boot |  Tap  | Extra |  Base |       |  |  Redo | Paste |  Copy |  Cut  |  Undo |
        |       |2x lock|2x lock|2x lock|       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  |  Left |  Down |   Up  | Right | CapWrd|
        |       |       |       |       |       |  |       |       |       |       | S:Caps|
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|  Boot |       |  ROpt |  Num  |  Nav  |       |  |  Home |  PgDn |  PgUp |  End  |  Ins  |  Undo |
|       |       |       |2x lock|2x lock|       |  |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |       |       |       |  | Enter |  Bksp |  Del  |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Space**. vi-style arrows (M N E I sit where h j k l are on QWERTY).
Thumbs repeat Enter / Bksp / Del as plain keys, so they **auto-repeat** when held.
O = Caps Word (Shift + O = Caps Lock).

### 5 Mouse

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Boot |  Tap  | Extra |  Base |       |  |  Redo | Paste |  Copy |  Cut  |  Undo |
        |       |2x lock|2x lock|2x lock|       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  | Mouse | Mouse | Mouse | Mouse |  Back |
        |       |       |       |       |       |  |  Left |  Down |   Up  | Right | Btn 4 |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|  Boot |       |  ROpt |  Sym  | Mouse |       |  | Scroll| Scroll| Scroll| Scroll|  Fwd  |  Undo |
|       |       |       |2x lock|2x lock|       |  |  Left |  Down |   Up  | Right | Btn 5 |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |       |       |       |  | Click | Click | Click |
                        |       |       |       |  | Right |  Left | Middle|
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Tab**. Pointer accelerates to full speed over 1.5 s. O = Back (button 4),
/ = Forward (button 5); they go back/forward in browsers, some Mac apps (e.g. Safari,
Finder) may ignore them. Thumbs: Enter=right click, Bksp=left click, Del=middle click.

### 6 Media

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Boot |  Tap  | Extra |  Base |       |  | (rgb) | (rgb) | (rgb) | (rgb) | (rgb) |
        |       |2x lock|2x lock|2x lock|       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  Cmd  |  Opt  |  Ctrl | Shift |       |  |  Prev |  Vol- |  Vol+ |  Next | (ext) |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|  Boot |       |  ROpt |  Fun  | Media |       |  |  BT 0 |  BT 1 |  BT 2 |  BT 3 | USB/BT| (rgb) |
|       |       |       |2x lock|2x lock|       |  |S:Clear|S:Clear|S:Clear|S:Clear| S:USB |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |       |       |       |  |  Stop |  Play |  Mute |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Esc**. BT 0–3 select Bluetooth profiles (Shift + BT = clear that profile's
pairing). USB/BT toggles the output (Shift = force USB).

### 7 Num

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   [   |   7   |   8   |   9   |   ]   |  |       |  Base | Extra |  Tap  |  Boot |
        |       |       |       |       |       |  |       |2x lock|2x lock|2x lock|       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   ;   |   4   |   5   |   6   |   =   |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   [   |   `   |   1   |   2   |   3   |   \   |  |       |  Num  |  Nav  |  ROpt |       |  Boot |
|       |       |       |       |       |       |  |       |2x lock|2x lock|       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |   .   |   0   |   -   |  |       |       |       |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Bksp**. Numpad on the left hand: `.`, `0`, `-` on the left thumbs.

### 8 Sym

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   {   |   &   |   *   |   (   |   }   |  |       |  Base | Extra |  Tap  |  Boot |
        |       |       |       |       |       |  |       |2x lock|2x lock|2x lock|       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   :   |   $   |   %   |   ^   |   +   |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   {   |   ~   |   !   |   @   |   #   |   |   |  |       |  Sym  | Mouse |  ROpt |       |  Boot |
|       |       |       |       |       |       |  |       |2x lock|2x lock|       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |   (   |   )   |   _   |  |       |       |       |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Enter**. The shifted versions of the Num layer: `(`, `)`, `_` on the left thumbs.

### 9 Fun

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  F12  |   F7  |   F8  |   F9  | PrtSc |  |       |  Base | Extra |  Tap  |  Boot |
        |       |       |       |       |       |  |       |2x lock|2x lock|2x lock|       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |  F11  |   F4  |   F5  |   F6  | ScrLk |  |       | Shift |  Ctrl |  Opt  |  Cmd  |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|  F12  |  F10  |   F1  |   F2  |   F3  | Pause |  |       |  Fun  | Media |  ROpt |       |  Boot |
|       |       |       |       |       |       |  |       |2x lock|2x lock|       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |  Menu | Space |  Tab  |  |       |       |       |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

Held from **Del**. F-keys laid out like the numpad. Menu / Space / Tab on the left thumbs.
PrtSc, ScrLk, Pause and Menu do nothing on macOS.

### Layer lock and switching keys

While holding a thumb key, the **same-hand** side has the "additional features" from
Miryoku:

| Key (left-hand layers: Nav, Mouse, Media) | Key (right-hand layers: Num, Sym, Fun) | Action |
|---|---|---|
| Q | ' | **Boot**: restart *this half* into the bootloader (**single tap!**) |
| W | Y | Switch to **Tap** base layer (double-tap) |
| F | U | Switch to **Extra** (QWERTY) base layer (double-tap) |
| P | L | Switch back to **Base** (double-tap); also unlocks a locked layer |
| D | H | Lock the layer you are holding (double-tap) |
| C | , | Lock the opposite-hand partner layer (double-tap): Nav↔Num, Mouse↔Sym, Media↔Fun |

> Boot is a single tap in this build (Miryoku can't double-tap-guard it on a split
> keyboard). The outer bottom pinky key on the same side is also Boot on those layers.
> If you enter the bootloader by accident (a USB drive appears), press the reset button
> once or unplug/replug; the keyboard restarts normally.

## How keys behave

| Thing | Behaviour in this build |
|---|---|
| Home-row mods and thumb layer keys | ZMK hold-tap, **tap-preferred, 200 ms**: released before 200 ms = tap, held longer = mod/layer. Pressing another key does not make it a hold early, so hold mods a moment before tapping the next key |
| Tap-then-hold repeat | **Off** (Miryoku design, keeps layer switching fast). Use the plain copies of Space/Bksp/Del/Enter/Tab on the Nav and Fun layers to auto-repeat |
| Mods on layers | Plain keys (no timing): hold the layer thumb, then mods, then the key, in any order |
| Layer lock / base switch | Double-tap within 200 ms |
| Caps Word | Capitalises letters until a key other than a letter, number, `_`, Bksp or Del is pressed (e.g. Space, `.`, `-`). Tap the Caps Word key again to stop early. Shift + Caps Word key = real Caps Lock |
| Shift functions | Shift + BT n = select and clear profile n. Shift + USB/BT = force USB. Shift + Caps Word = Caps Lock |
| Mouse keys | Movement max speed 1250, reached after 1.5 s (linear). Scroll speed 100 |
| Clipboard keys | Send ⌘Z / ⇧⌘Z / ⌘X / ⌘C / ⌘V (`MIRYOKU_CLIPBOARD_MAC`). **They don't work on Windows** (they would send Win+key); Windows would need `MIRYOKU_CLIPBOARD_WIN` instead |
| Key rollover | 6 keys at once plus modifiers (ZMK default HKRO report) |
| Debounce | 5 ms press / 5 ms release (ZMK default) |
| Combos | None |

## Bluetooth, USB and pairing

- **Profiles:** the left half can remember 5 computers/phones (profiles 0–4). The Media layer
  has keys for profiles 0–3 (K, H, comma, dot). Profile 4 has no key.
- **Pair a new device:** hold Esc, tap a free profile key (or clear one with Shift), then
  pair "Shaon's ToTeM" from the computer's Bluetooth settings.
- **Forget one pairing:** hold Esc, hold T (Shift), tap that profile's key. Then remove
  the keyboard on the computer too.
- **USB vs Bluetooth:** when plugged into a computer by USB, ZMK sends keys over USB.
  Hold Esc and tap / to toggle; hold Esc + T (Shift) and tap / to force USB.
- **The two halves** pair with each other automatically the first time both run this
  firmware. That pairing is stored separately and isn't affected by clearing a profile.
- **Name:** `Shaon's ToTeM` (max 16 characters). `CONFIG_BT_DEVICE_NAME_DYNAMIC=n` makes
  the keyboard always use the name from the firmware instead of the copy saved on its
  first boot, so renaming only needs a reflash. A Mac may keep showing the old name for
  an existing pairing until you forget and re-pair.
- **Transmit power:** ZMK default (0 dBm). For a weak connection add
  `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` to `config/totem.conf` (+8 dBm, negligible battery cost).

## Battery and sleep

- **Battery levels:** the left half reports its own battery and, as a second battery,
  the right half's (`config/totem_left.conf`). Updated every 60 s. macOS itself only
  shows one; [ZMK Battery Bar](https://github.com/itouuuuuuuuu/zmk-battery-bar)
  (`brew install --cask itouuuuuuuuu/tap/zmk-battery-bar`, macOS 14+) shows both in the
  menu bar. If it shows only one, forget and re-pair the keyboard on the Mac.
- **Idle:** after 30 s without a key press the keyboard enters a light idle state
  (nothing visible on this keyboard).
- **Deep sleep:** after **4 hours** without a key press (`CONFIG_ZMK_IDLE_SLEEP_TIMEOUT`).
  Bluetooth disconnects and power use drops to almost nothing. Press any key to wake;
  that key press is not typed, and reconnecting takes a few seconds. Each half sleeps on
  its own (the right half counts only its own key presses).
- **Battery cost of the 4 h timeout:** awake but idle, the left half draws roughly
  0.5 mA and the right half roughly 0.02 mA (ZMK's estimates), so leaving the keyboard
  costs the left half up to about 2 mAh before it sleeps.

## ZMK Studio (live keymap editing)

[ZMK Studio](https://zmk.dev/docs/features/studio) lets you change keys and layers
without rebuilding firmware.

1. Open <https://zmk.studio> in Chrome or Edge, or install the desktop app from
   <https://github.com/zmkfirmware/zmk-studio/releases> (macOS `.dmg`).
2. Connect to the **left half** over USB or Bluetooth (Studio only runs on the left half).
3. Edit, then **Save**. Changes are stored on the keyboard.

- No unlock key is needed (`CONFIG_ZMK_STUDIO_LOCKING=n`).
- **Saved Studio changes override the keymap in this repo.** After flashing new firmware
  with keymap changes, they won't appear until you click **Restore Stock Settings** in
  Studio, which throws away the Studio edits.
- The on-screen key shapes come from the physical layout in
  `boards/shields/totem/totem.dtsi`, made from the official TOTEM QMK `keyboard.json`.

## Getting and flashing firmware

**Download:** every push to `master` builds both halves and replaces the
[`latest` release](../../releases/latest) with `totem_left.uf2` and `totem_right.uf2`.
Every run (also pull requests) has a `firmware` artifact under **Actions**. A manual
build: **Actions → Build ZMK firmware → Run workflow**.

**Flash** (repeat for each half; left file on the left half, right file on the right half):

1. Connect the half to the computer with USB.
2. Enter the bootloader: **double-press the reset button** on the XIAO, or use the
   keyboard (left: hold Space, tap Q; right: hold Enter, tap ').
3. A USB drive appears (usually named `XIAO-SENSE`).
4. Copy the `.uf2` file onto it. The half restarts by itself when the copy finishes
   (macOS may complain that the drive was ejected; that's normal).

**Build locally** (needs Docker; produces `local-build/totem_left.uf2` and
`local-build/totem_right.uf2`, which git ignores). Run in the repo root:

```sh
docker run --rm -v "$PWD:/repo" -w /repo zmkfirmware/zmk-build-arm:stable sh -c '
  set -e
  mkdir -p /w && cp -R config /w/ && cd /w
  west init -l config && west update --fetch-opt=--filter=tree:0 && west zephyr-export
  west build -s zmk/app -d build/left -b seeeduino_xiao_ble -S studio-rpc-usb-uart -- \
    -DZMK_CONFIG=/w/config -DZMK_EXTRA_MODULES=/repo -DSHIELD=totem_left \
    -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
  west build -s zmk/app -d build/right -b seeeduino_xiao_ble -- \
    -DZMK_CONFIG=/w/config -DZMK_EXTRA_MODULES=/repo -DSHIELD=totem_right
  mkdir -p /repo/local-build
  cp build/left/zephyr/zmk.uf2 /repo/local-build/totem_left.uf2
  cp build/right/zephyr/zmk.uf2 /repo/local-build/totem_right.uf2'
```

## Changing the configuration

| What | Where |
|---|---|
| Miryoku options (alphas, nav style, clipboard, layer overrides) | `config/custom_config.h` |
| ZMK settings for both halves (name, sleep, mouse keys) | `config/totem.conf` |
| ZMK settings for the left half only (battery proxy) | `config/totem_left.conf` |
| ZMK Studio on/off, which halves are built | `build.yaml` |
| ZMK version | `config/west.yml` **and** `.github/workflows/build.yml` |
| Keyboard hardware definition (pins, matrix, physical layout) | `boards/shields/totem/` |
| Miryoku itself (vendored copy, avoid editing) | `config/miryoku/` |

### Current settings, file by file

`config/custom_config.h`

| Setting | Effect |
|---|---|
| `MIRYOKU_NAV_VI` | vi-style arrows on Nav (and matching Mouse layout) |
| `MIRYOKU_CLIPBOARD_MAC` | Clipboard keys send ⌘ shortcuts |
| `MIRYOKU_LAYER_MOUSE` (override) | Miryoku's vi Mouse layer plus Back (O) and Forward (/) |
| (not set) | Alphas = Colemak-DH, Extra = QWERTY, Tap = Colemak-DH, layers not flipped |

`config/totem.conf` (both halves)

| Setting | Effect |
|---|---|
| `CONFIG_ZMK_POINTING=y` | Mouse keys |
| `CONFIG_ZMK_SLEEP=y`, `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=14400000` | Deep sleep after 4 h |
| `CONFIG_ZMK_KEYBOARD_NAME="Shaon's ToTeM"` | Bluetooth and USB name |
| `CONFIG_BT_DEVICE_NAME_DYNAMIC=n` | Always use the firmware name (renames apply after a reflash) |
| `# CONFIG_USB_DEVICE_MANUFACTURER` | Commented out (ZMK default manufacturer) |

`config/totem_left.conf` (left half only)

| Setting | Effect |
|---|---|
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_FETCHING=y` | Left half reads the right half's battery |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_PROXY=y` | …and reports it to the computer |

`build.yaml`

| Entry | Effect |
|---|---|
| `seeeduino_xiao_ble` + `totem_left`, snippet `studio-rpc-usb-uart`, `CONFIG_ZMK_STUDIO=y`, `CONFIG_ZMK_STUDIO_LOCKING=n` | Left half with ZMK Studio (USB and Bluetooth), unlocked |
| `seeeduino_xiao_ble` + `totem_right` | Right half |

### Miryoku options

Add `#define` lines to `config/custom_config.h`.

| Option | Values |
|---|---|
| Alphas | `MIRYOKU_ALPHAS_` + `AZERTY` `BEAKL15` `COLEMAK` `COLEMAKDHK` `DVORAK` `HALMAK` `QWERTY` `QWERTZ` `WORKMAN` (default Colemak-DH) |
| Extra / Tap layers | `MIRYOKU_EXTRA_<ALPHAS>`, `MIRYOKU_TAP_<ALPHAS>` |
| Nav | `MIRYOKU_NAV_VI` (current), `MIRYOKU_NAV_INVERTEDT` |
| Clipboard | `MIRYOKU_CLIPBOARD_MAC` (current), `MIRYOKU_CLIPBOARD_WIN`, `MIRYOKU_CLIPBOARD_FUN` |
| Layers | `MIRYOKU_LAYERS_FLIP` (swap which hand has the layer thumbs) |
| Replace a layer | `#define MIRYOKU_LAYER_<NAME> …` with 40 bindings, see the Mouse override |

See the [Miryoku reference](https://github.com/manna-harbour/miryoku/tree/master/docs/reference).

### Key position numbers

Used by ZMK for combos, hold-trigger positions, etc. (`boards/shields/totem/totem.dtsi`).

```
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   0   |   1   |   2   |   3   |   4   |  |   5   |   6   |   7   |   8   |   9   |
        |       |       |       |       |       |  |       |       |       |       |       |
        +-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+
        |   10  |   11  |   12  |   13  |   14  |  |   15  |   16  |   17  |   18  |   19  |
        |       |       |       |       |       |  |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
|   20  |   21  |   22  |   23  |   24  |   25  |  |   26  |   27  |   28  |   29  |   30  |   31  |
|       |       |       |       |       |       |  |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+  +-------+-------+-------+-------+-------+-------+
                        |   32  |   33  |   34  |  |   35  |   36  |   37  |
                        |       |       |       |  |       |       |       |
                        +-------+-------+-------+  +-------+-------+-------+
```

### Upgrading ZMK

The build is pinned to ZMK v0.3 (`revision: v0.3` in `config/west.yml` and
`build-user-config.yml@v0.3` in `.github/workflows/build.yml`; change both together).
Moving to v0.4 or newer (Zephyr 4.1) also requires renaming the board in `build.yaml`
and the local build command from `seeeduino_xiao_ble` to `xiao_ble//zmk`. Read the ZMK
blog for breaking changes first: <https://zmk.dev/blog>.

## Troubleshooting

| Problem | Fix |
|---|---|
| Mac shows the keyboard connected but nothing types | Forget the keyboard in macOS Bluetooth settings. On the keyboard: hold Esc + T, tap the profile's key. Pair again |
| Halves don't talk to each other (right half dead) | Reset both halves at the same time. If that fails, do a full settings reset (below) |
| Keymap changes from this repo don't show up | ZMK Studio edits override them: Studio → **Restore Stock Settings** |
| Stuck in the Tap layer or a strange layer | Press the left half's reset button once |
| A USB drive appeared on its own | You hit Boot. Press reset once or unplug/replug |
| Bluetooth drops / poor range | Add `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` to `config/totem.conf` |
| Keyboard "disconnects" after a long break | Deep sleep (4 h). Press any key and wait a few seconds |
| Clipboard keys do something odd on Windows | Expected: they send ⌘ (Win) shortcuts, see clipboard above |

**Full settings reset** (wipes all Bluetooth pairings and ZMK Studio edits on a half):

1. Add to `build.yaml` and push:
   ```yaml
     - board: seeeduino_xiao_ble
       shield: settings_reset
   ```
2. Download `settings_reset-seeeduino_xiao_ble-zmk.uf2` from the run's `firmware`
   artifact (it is not in the release).
3. Flash it to **both** halves, then flash the normal `totem_left.uf2` / `totem_right.uf2`.
4. The halves pair with each other again; re-pair the computer.
5. Remove the `settings_reset` entry again.

## Repository layout

```
.github/workflows/build.yml  # CI: builds firmware, publishes the "latest" release
build.yaml                   # What to build: left (with Studio) and right half
zephyr/module.yml            # Makes this repo a Zephyr module so boards/ is found
boards/shields/totem/        # TOTEM hardware: pins, matrix, physical layout
config/
  west.yml                   # ZMK version (v0.3)
  totem.keymap               # Keymap entry point: custom_config.h + Miryoku
  custom_config.h            # Miryoku options and the Mouse layer override
  totem.conf                 # ZMK settings for both halves
  totem_left.conf            # ZMK settings for the left half only
  miryoku/                   # Vendored Miryoku ZMK core and the TOTEM mapping
```

## Credits

- Miryoku and Miryoku ZMK by [Manna Harbour](https://github.com/manna-harbour)
- TOTEM keyboard and shield by [GEIGEIGEIST](https://github.com/GEIGEIGEIST),
  vendored from [zmk-config-totem@8325195](https://github.com/GEIGEIGEIST/zmk-config-totem/tree/83251955e2e2777bf82266922f688afddf1d7870/config/boards/shields/totem).
  The physical layout was added from the official
  [TOTEM QMK `keyboard.json`](https://github.com/GEIGEIGEIST/TOTEM/blob/main/firmware/QMK/VIAL_source/totem/keyboard.json).
- [ZMK](https://zmk.dev) firmware
