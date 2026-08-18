## Install and Configure `gammastep` (Wayland/Sway)

`gammastep` is a screen color temperature tool, a Redshift fork with native Wayland support via the `wlr-gamma-control` protocol, which `sway` implements. It shifts the display to warmer tones at night to reduce eye strain and blue light exposure.

**Step 1: Install gammastep**

```bash
sudo pacman -S gammastep
```

**Step 2: Create the config file**

```bash
mkdir -p ~/.config/gammastep
nano ~/.config/gammastep/config.ini
```

```ini
[general]
; Color temperature to use in day and night
temp-day=6500
temp-night=3700

; Solar elevation angle to use night mode
; Negative values use dawn/dusk, positive use a stricter cutoff
; adjustment-method=wayland is auto-detected under Sway, no need to set it

; Use manual latitude/longitude instead of a location provider
location-provider=manual

[manual]
lat=41.0
lon=29.0
```

> [!NOTE]
Replace `lat`/`lon` with your actual coordinates (two decimal places is enough). This avoids the `geoclue2` location service, which needs an extra daemon and permissions to work under Wayland.

**Step 3: Test it manually**

```bash
gammastep
```

You should see the screen shift color temperature within a few seconds. Press `Ctrl+C` to stop and reset to normal.

**Step 4: Autostart with Sway**

Add this line to `~/.config/sway/config`:

```
exec gammastep
```

Reload Sway (`$mod+Shift+c` by default, or `swaymsg reload`) to pick up the change.

**Step 5: Verify it's running**

```bash
pgrep -a gammastep
```

## Why `exec` in the Sway config over a systemd user service

`gammastep` needs a running Wayland compositor to talk to via `wlr-gamma-control`, so it only makes sense to start after Sway is up. Launching it with `exec` in the Sway config ties its lifecycle directly to the compositor session: it starts when Sway starts and dies when Sway exits, with no risk of racing a systemd unit against compositor startup or leaking a stale process into the next session.

## Troubleshooting

If the screen doesn't shift, confirm the compositor is recognized:

```bash
gammastep -v
```

The verbose output should show `Using method 'wayland'`. If it falls back to a different method or errors out, confirm `sway` is the active session and that `gammastep` was installed after Wayland support was added (it has shipped in the official package for a while, so this is unlikely on a fresh install).

If `location-provider=manual` is skipped in favor of geoclue2, double check there's no stray `[manual]`-less config or typo in the `location-provider` line, gammastep silently falls back to automatic location lookup otherwise.
