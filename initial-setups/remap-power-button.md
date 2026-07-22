## Remap the Power Button: Short Press = Suspend, Long Press = Poweroff

By default, `systemd-logind` powers off the machine on any power button press. On this laptop we run Sway (no GNOME/KDE session daemon grabbing input events), so `logind` handles the physical power button directly, no compositor keybinding needed.

Requires `systemd >= 254` for `HandlePowerKeyLongPress`. Check with `systemctl --version`.

**Step 1: Create a logind drop-in**

```bash
sudo mkdir -p /etc/systemd/logind.conf.d
sudo nano /etc/systemd/logind.conf.d/10-power-button.conf
```

**Step 2: Set short-press vs long-press behavior**

```ini
[Login]
HandlePowerKey=suspend
HandlePowerKeyLongPress=poweroff
```

**Step 3: Apply it**

```bash
sudo systemctl restart systemd-logind
```

> [!NOTE]
> Restarting `systemd-logind` can occasionally hiccup an active graphical session on some setups. If you'd rather not risk that, reboot instead and the drop-in takes effect on the next boot.

**Step 4: Verify**

```bash
loginctl show-session $(loginctl | awk '/seat0/{print $1}' | head -1) -p HandlePowerKey -p HandlePowerKeyLongPress
```

Then test on real hardware: a short press should suspend, holding it down for a few seconds should power off. The long-press threshold itself isn't configurable in `logind.conf`, it's fixed internally.

**Troubleshooting**

If nothing happens on short press, check for a conflicting `bindsym XF86PowerOff` in the Sway config. `logind` normally grabs the power button at the input-device level before Sway ever sees it, but it's worth ruling out.

`HandleRebootKey` / `HandleRebootKeyLongPress` exist too, for a separate reboot combo if you ever want one.

If you want to see live logs of button behavoiur:

```bash
sudo journalctl -u systemd-logind.service -f
```
Press your power button. You should instantly see text like Power key pressed appear in the log stream.

Or, for log history:

```bash
sudo journalctl -u systemd-logind.service | grep -i "power"
```

## Why `logind.conf.d/` over `HandlePowerKey=ignore` + a Sway keybinding

The alternative I considered: set `HandlePowerKey=ignore` in `/etc/systemd/logind.conf` directly, then bind the key in Sway instead:

```
bindsym --locked XF86PowerOff exec systemctl suspend
```

**Why not:** that only covers the short-press case. `HandlePowerKey=ignore` turns off all of `logind`'s handling of the button, and a plain `bindsym` fires the same action on every press regardless of how long the button is held, there's no built-in way to tell a tap from a hold. Getting the long-press-poweroff behavior out of that setup would mean hand-rolling hold-duration detection in Sway (a `bindsym` on press starting a timer, a `bindsym --release` branching on elapsed time), which just reimplements, in shell, logic `logind` already has natively via `HandlePowerKeyLongPress` (systemd >= 254).

It's also compositor-scoped: a Sway keybinding only fires while Sway is running and has input focus. Pressing the power button at the `ly` greeter, from a bare TTY, or before Sway starts does nothing, since `logind` isn't listening (it's set to `ignore`) and Sway isn't running yet to catch it either.

There's a secondary reason to prefer a `logind.conf.d/` drop-in over editing `/etc/systemd/logind.conf` directly, regardless of the above: that file is pacman-owned. A future `systemd` update that ships a new default leaves a `.pacnew` to merge by hand. A drop-in is never touched by package upgrades and stays a small, self-contained, purpose-named override.
