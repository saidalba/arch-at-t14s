## Remap HHKB Keys with `keyd` (Device-Specific Config)

Before starting: I use HHKB Professional Hybrid Type-S US layout model. And almost like all other HHKB models avaible out there it has DIP Switches. I use DP SW1 & SW2 set as OFF which puts your keyboard into HHK/Unix mode. I prefer using my keyboard in Unix mode since I'm swapping between computers with eKVM switch. Currently I am connected to linux and macos computers and in order to get best of both worlds, putting your keyboard Unix mode(DIP SW1 & SW2 OFF) is the best in my experience. You can configure your modifier keys easily with `keyd` on linux and `karabiner-elements` on macos.

> [!NOTE]
If you turn on DIP SW1 or SW2, you would put your keyboard on Win Mode or MacOS mode respectively. In those modes I found it is harder to map your keys since it ruterns duplicate scan codes for different keys. (e.g when SW2 ON(MacOS mode) both `left-alt` and `right-meta` keys returns --> `leftmeta`) you can simulate this behaviour via `keyd monitor` with tweaking your switches. But if you want to save time, and use your keyboard in multiple operating systems best option is Unix mode(SW1 & SW2 OFF).

You can find DIP Switch layouts here: [HHKB Pro Hybrid Tpye-S User's Guide](https://origin.pfultd.com/downloads/hhkb/manual/P3PC-6641-05EN.pdf)
If you want to dive deep in this issue and do more research you can start from here: [reddit-post](https://www.reddit.com/r/archlinux/comments/1fjjg74/ive_written_a_userfriendly_guide_to_remapping_the/) and here [keyboard-remap-guide](https://github.com/Alekamerlin/keyboard-remap-guide)

Goal: remap the HHKB's Japanese-layout keys (`henkan`, `muhenkan`, the left `◇` key) and add a tap/hold arrow layer on `;`, scoped to this keyboard only via its USB vendor:product ID, so the mappings don't apply to the laptop's internal keyboard.

This builds on `remap-keyboard-keys.md`, which already installs and enables `keyd` system-wide. If `keyd` isn't installed yet, do that first.

Mappings on this machine:

- `henkan` (right of spacebar) -> `rightmeta`
- `muhenkan` (left of spacebar) -> `leftmeta`
- `leftmeta` (the physical `◇` key, left of `muhenkan`) -> `leftalt`
- `semicolon` -> tap for `;`, hold for an arrow layer (`h`/`j`/`k`/`l` -> arrows)

**Step 1: Find the HHKB's vendor:product ID**

```bash
sudo keyd monitor
```

Plug in (or unplug/replug) the HHKB and look for its entry, e.g. `id: 04fe:0021`, `name: PFU Limited HHKB...`. `lsusb` shows the same ID if you want to cross-check.

**Step 2: Write a device-specific config**

```bash
sudo nano /etc/keyd/hhkb.conf
```

```ini
[global]
# Fine-tune the hold vs tap delay (in milliseconds).
# Uncomment and/or increase if you get accidental arrows while typing text quickly.
# overload_tap_timeout = 180

[ids]
04fe:0021

[main]
# --- Keys to the RIGHT of spacebar ---
# Maps the physical Japanese '変換' key to Right Meta (Super)
henkan = rightmeta

# --- Keys to the LEFT of spacebar ---
# Maps the physical Japanese '無変換' key to Left Meta (Super)
muhenkan = leftmeta

# Maps the physical Left "◇" key to Right Alt (AltGr / Option)
leftmeta = leftalt

# Hold semicolon for arrows, tap it for a regular semicolon
semicolon = overload(nav, semicolon)

[nav]
h = left
j = down
k = up
l = right
```

**Step 3: Reload**

```bash
sudo keyd reload
```

**Step 4: Verify**

```bash
sudo keyd monitor
```

Press each remapped key and confirm the output events match what's expected. For the `semicolon` overload, a quick tap should emit `;`, holding it plus `h`/`j`/`k`/`l` should emit arrow keys instead.

## Why a separate `hhkb.conf` over adding these lines to `default.conf`

`default.conf` (see `remap-keyboard-keys.md`) uses `[ids] *`, so it applies to every keyboard, including the laptop's internal one. The HHKB's Japanese-layout keys (`henkan`, `muhenkan`) don't exist on the internal keyboard, and the `semicolon` arrow layer is a preference specific to typing on the HHKB, not something wanted system-wide on every keyboard plugged into the machine.

Scoping this file to `[ids] 04fe:0021` means `keyd` only applies it when that exact device is present. `keyd` matches a device's most specific `[ids]` entry across all loaded config files, so an exact vendor:product match here takes priority over the wildcard in `default.conf` for the HHKB specifically, while the internal keyboard keeps using `default.conf` untouched. Confirm this precedence behavior for the installed `keyd` version with `man keyd.conf`, since matching rules have evolved across releases.

## Troubleshooting

See `remap-keyboard-keys.md` for general `keyd` service troubleshooting (`keyd.socket` connection errors, checking `journalctl -u keyd`).

If the HHKB-specific mappings don't apply but `default.conf`'s mappings do, double check the vendor:product ID in `[ids]` against `sudo keyd list-keyboards` again. Some HHKB models/firmware revisions may report a different ID, or the same physical key names (`henkan`, `muhenkan`) may not be recognized if the keyboard is in a different DIP-switch mode (e.g. Mac mode vs. standard mode).
