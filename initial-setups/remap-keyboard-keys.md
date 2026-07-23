## Remap Modifier and Other Keys System-Wide with `keyd`

Goal: remap caps lock, the left modifier keys, and the right alt / Copilot key, system-wide (TTY, greeter, and every session), not just inside Sway.

Mappings on this machine:

- `capslock` <-> `leftcontrol` (swap)
- `leftalt` <-> `leftmeta` (swap)
- `rightalt` -> `rightmeta`
- Copilot key -> `rightalt`

**Step 1: Install keyd**

```bash
paru -S keyd
```

**Step 2: Enable the service**

```bash
sudo systemctl enable --now keyd
```

**Step 3: Write a config**

```bash
sudo mkdir -p /etc/keyd
sudo nano /etc/keyd/default.conf
```

```ini
[ids]
*

[main]
capslock = leftcontrol
leftcontrol = capslock

leftalt = leftmeta
leftmeta = leftalt

rightalt = rightmeta
leftmeta+leftshift+f23 = rightalt
```

**Step 4: Reload**

```bash
sudo keyd reload
```

**Step 5: Verify**

```bash
sudo keyd monitor
```

Press each remapped key and confirm the output events match what's expected before relying on it day to day.

## The Copilot key is not a real keycode

This keyboard's Copilot key has no dedicated Linux keycode. `sudo keyd monitor` shows it firing as a chord: `leftmeta down, leftshift down, f23 down, f23 up, leftshift up, leftmeta up`. This is the OEM firmware faking the Windows-standard fallback shortcut (`Win+Shift+F23`) at the scan-code level, since Microsoft's spec lets OEMs ship a Copilot key without a dedicated kernel driver everywhere. That synthesis happens before the kernel's input subsystem, let alone `keyd`, ever sees anything, so by the time it reaches evdev it's three indistinguishable key events, identical to a human pressing all three by hand.

`f23` is the only part of that sequence unique to this key on this keyboard, which is why it's the one to key a mapping off of.

## Why `leftmeta+leftshift+f23 = rightalt` (a chord) over a plain `f23 = rightalt` remap

**Why:** a plain `f23 = rightalt` line only renames the `f23` event. It does nothing about the `leftmeta` and `leftshift` events bundled in the same hardware chord, so those still pass through untouched, and given the `leftalt <-> leftmeta` swap above, that means every Copilot key press would leak a real `Alt+Shift` press/release into whatever app has focus before `rightalt` fires. Under Sway this is likely harmless, since bindings fire on an actual bound key rather than on modifiers held alone, but it's still an unintended side effect that any app reacting to raw modifier press/release (some layout switchers do) could pick up on.

The chord form (`leftmeta+leftshift+f23 = rightalt`) tells `keyd` to treat all three keys arriving together as one atomic unit and emit only `rightalt`, swallowing the `leftmeta`/`leftshift` events instead of letting them leak through. Confirm the exact chord syntax and timeout behavior for the installed `keyd` version with `man keyd.conf`, since this feature has evolved across releases.

## Why `keyd` over Sway's `xkb_options`

Sway can do a handful of built-in swaps (e.g. `capslock:swapescape`) via `xkb_options` in its config, but that only takes effect once Sway is running and has input focus. `keyd` operates at the input-device level (the same reasoning as `logind` handling the power button, see `remap-power-button.md`), so the remap is active at a bare TTY, at the `ly` greeter, and in every compositor or session, not just inside Sway. It also isn't limited to the small set of swaps `xkb_options` supports, which matters here since several of these mappings (the Copilot key chord in particular) need more than a simple 1:1 swap.

## Troubleshooting

**`ERROR: Failed to connect to "/var/run/keyd.socket"...` on `keyd reload`**

This means the daemon isn't running, either because it was never started or because it crashed on a bad config. Check:

```bash
sudo systemctl status keyd
sudo journalctl -u keyd -b --no-pager | tail -50
```

If `keyd.service` shows `inactive (dead)`, start it:

```bash
sudo systemctl enable --now keyd
```

If it started and then died, the journal will show the config parse error, including which line it choked on.

## Reference: Primary modifier eys in major OS


| Function | Windows | macOS | Linux |
| :--- | :--- | :--- | :--- |
| **Primary Command** | `Ctrl` | `Cmd` (⌘) | `Ctrl` |
| **Secondary Command** | `Alt` | `Option` (⌥) | `Alt` |
| **System / Application Launcher** | `Windows Key` (⊞) | `Cmd` (⌘) | `Super` (or `Meta`) |
| **Terminal / App Context** | `Ctrl` | `Ctrl` | `Ctrl` |
| **Alternate Graphics** | `AltGr` | `Option` (⌥) | `AltGr` |
