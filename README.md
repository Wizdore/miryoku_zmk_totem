# Miryoku ZMK – TOTEM

[Miryoku](https://github.com/manna-harbour/miryoku) layout for the
[TOTEM](https://github.com/GEIGEIGEIST/TOTEM) 38-key split keyboard, built with
[ZMK](https://zmk.dev) for the Seeed XIAO nRF52840 (`xiao_ble`).

## Building

Firmware is built by GitHub Actions on every push, or manually from
**Actions → Build ZMK firmware → Run workflow**.

Builds of the default branch are published to the
[`latest` release](../../releases/latest), which is replaced on every build:

- `totem_left.uf2`: left half (central, connects to the host, ZMK Studio enabled)
- `totem_right.uf2`: right half

Every run, including pull requests, also has a `firmware` artifact with the same files.

## Flashing

1. Connect a half over USB and double-tap its reset button. A USB drive appears.
2. Copy the matching `.uf2` file onto the drive. The board reboots when the copy finishes.
3. Repeat for the other half.

## Layout

```
.github/workflows/build.yml  # CI: builds firmware and publishes the release
build.yaml                   # Build matrix (board + shields, Studio on left)
zephyr/module.yml            # Makes this repo a Zephyr module so boards/ is found
boards/shields/totem/        # TOTEM shield definition and physical layout
config/
  west.yml                   # ZMK version (pinned to v0.3)
  totem.keymap               # Keymap entry point (pulls in Miryoku)
  totem.conf                 # Kconfig options for both halves (name, sleep, pointing, ...)
  totem_left.conf            # Kconfig options for the left half only (battery proxy)
  custom_config.h            # Miryoku options
  miryoku/                   # Miryoku ZMK core and TOTEM mapping
```

## Customising

### ZMK Studio

The keymap can be edited live from the browser with [ZMK Studio](https://zmk.studio):

1. Open <https://zmk.studio> in a Chromium-based browser (Chrome, Edge).
2. Connect the left half over USB (or Bluetooth) and select it.
3. Edit keys and layers, then **Save** to store the changes on the keyboard.

Studio is enabled only on the left (central) half, with locking disabled, so no
unlock key is needed. Changes made in Studio are stored on the keyboard and take
priority over `config/totem.keymap`. Use **Restore Stock Settings** in Studio to
return to the Miryoku keymap from this repo.

### Miryoku options

Add or remove `#define` lines in [`config/custom_config.h`](config/custom_config.h).
The current config sets `MIRYOKU_NAV_VI`.

| Option    | Defines                                                                                   |
|-----------|-------------------------------------------------------------------------------------------|
| Alphas    | `MIRYOKU_ALPHAS_` + `AZERTY` `BEAKL15` `COLEMAK` `COLEMAKDHK` `DVORAK` `HALMAK` `QWERTY` `QWERTZ` `WORKMAN` (default: Colemak-DH) |
| Nav       | `MIRYOKU_NAV_VI`, `MIRYOKU_NAV_INVERTEDT`                                                 |
| Layers    | `MIRYOKU_LAYERS_FLIP`                                                                     |
| Clipboard | `MIRYOKU_CLIPBOARD_FUN`, `MIRYOKU_CLIPBOARD_MAC`, `MIRYOKU_CLIPBOARD_WIN`                 |
| Extra/Tap | `MIRYOKU_EXTRA_<ALPHAS>`, `MIRYOKU_TAP_<ALPHAS>`                                          |

See the [Miryoku reference](https://github.com/manna-harbour/miryoku/tree/master/docs/reference)
for details.

### ZMK settings

Edit [`config/totem.conf`](config/totem.conf) for both halves, or
[`config/totem_left.conf`](config/totem_left.conf) for the left half only. See the
[ZMK configuration docs](https://zmk.dev/docs/config) for available options.

### Battery levels

The left half reports both batteries to the computer. Most operating systems only
show the first one; use an app such as
[ZMK Battery Bar](https://github.com/itouuuuuuuuu/zmk-battery-bar) (macOS) to see both.

### ZMK version

This config is pinned to ZMK [v0.3](https://github.com/zmkfirmware/zmk/releases/tag/v0.3.0),
the latest release. To upgrade, change `revision` in [`config/west.yml`](config/west.yml)
and the `@ref` in [`.github/workflows/build.yml`](.github/workflows/build.yml) to the
same tag. From v0.4 (Zephyr 4.1) the board in [`build.yaml`](build.yaml) must also be
renamed from `seeeduino_xiao_ble` to `xiao_ble//zmk`.

## Credits

- Miryoku and Miryoku ZMK by [Manna Harbour](https://github.com/manna-harbour)
- TOTEM keyboard and shield by [GEIGEIGEIST](https://github.com/GEIGEIGEIST),
  vendored from [zmk-config-totem@8325195](https://github.com/GEIGEIGEIST/zmk-config-totem/tree/83251955e2e2777bf82266922f688afddf1d7870/config/boards/shields/totem).
  The physical layout was added from the official
  [TOTEM QMK `keyboard.json`](https://github.com/GEIGEIGEIST/TOTEM/blob/main/firmware/QMK/VIAL_source/totem/keyboard.json).
