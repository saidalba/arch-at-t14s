## Power Management: Options and Alternatives

There are multiple options for power and battery management on Linux. The most common ones are TLP and `power-profiles-daemon` (PPD). Pick one and stick with it.

### Why You Can't Use Both

TLP and `power-profiles-daemon` (PPD) are both power management tools, but **they cannot run at the same time**. Both try to control the same hardware settings, which causes conflicts: you may see warning messages in your system logs, and desktop environments like GNOME will often disable battery-related settings entirely once they detect two tools fighting for control.

## Approach 1: PPD + udev/sysfs

To avoid conflicts, we'll use:

- **PPD**: for CPU performance modes and desktop integration (it has a native UI and works well with most desktop environments).
- **A `udev`/`sysfs` rule**: for setting battery charge thresholds, something PPD doesn't handle on its own.

This gives you the best of both: native performance switching, custom battery limits, and no background conflicts.

**Step 1: Install and enable PPD**

```bash
sudo systemctl enable --now power-profiles-daemon
```

**Step 2: Set a permanent battery charge limit**

PPD doesn't manage battery charge thresholds, so we'll create a config file that sets them automatically every time the system boots.

1. Create the config file:

   ```bash
   sudo nano /etc/tmpfiles.d/battery-thresholds.conf
   ```

2. Paste in the following (this means: start charging at 75%, stop at 80%):

   ```
   w /sys/class/power_supply/BAT0/charge_control_start_threshold - - - - 75
   w /sys/class/power_supply/BAT0/charge_control_end_threshold - - - - 80
   ```

   Want different limits? Just change `75` and `80` to your preferred start/stop percentages.

3. Save and exit (`Ctrl+O`, then `Enter`, then `Ctrl+X`).

**Step 3: Apply the settings immediately**

You don't need to reboot, this command applies the file right away:

```bash
sudo systemd-tmpfiles --create /etc/tmpfiles.d/battery-thresholds.conf
```

**Step 4: Verify everything is working**

Check PPD is active:

```bash
powerprofilesctl
```

You should see an asterisk (`*`) next to your active profile (usually `balanced`).

Check your battery threshold:

```bash
cat /sys/class/power_supply/BAT0/charge_control_end_threshold
```

This should print `80` (or whatever value you set).

### Switching Power Profiles

List available profiles (usually `performance`, `balanced`, and `power-saver`):

```bash
powerprofilesctl list
```

Switch to a profile:

```bash
powerprofilesctl set power-saver
```

Replace `power-saver` with `balanced` or `performance` as needed.

### Automatically Switch Power Profiles on AC[Plugged] or AC[Unplugged]

PPD doesn't switch profiles based on AC status by itself, so we'll pair it with a `udev` rule that fires a small script whenever the power supply changes state.

**Step 1: Create the switching script**

```bash
sudo nano /usr/local/bin/power-profile-switch.sh
```

Paste in the following (performance on AC, power-saver on battery):

```bash
#!/bin/bash

if grep -q 1 /sys/class/power_supply/AC*/online 2>/dev/null; then
    powerprofilesctl set performance
else
    powerprofilesctl set power-saver
fi
```

Save and exit, then make it executable:

```bash
sudo chmod +x /usr/local/bin/power-profile-switch.sh
```

> [!NOTE]
> Check your AC adapter's device name first with `ls /sys/class/power_supply/`. It's usually `AC` or `ADP1`; adjust the glob in the script if yours differs.

**Step 2: Create the udev rule**

```bash
sudo nano /etc/udev/rules.d/99-power-profile-on-ac.rules
```

Paste in:

```
SUBSYSTEM=="power_supply", ATTR{type}=="Mains", RUN+="/usr/local/bin/power-profile-switch.sh"
```

Save and exit.

**Step 3: Reload udev rules**

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=power_supply
```

The trigger command also applies the correct profile immediately, based on your current power state.

**Step 4: Verify it works**

Plug and unplug your charger, then check the active profile:

```bash
powerprofilesctl
```

The asterisk (`*`) should move to `performance` when plugged in and `power-saver` when on battery.

## Approach 2: PPD + batctl

This is my personal choice for now.

**Why:** it's easier to make changes, and `batctl` provides an abstraction layer over the raw sysfs paths. If you frequently move between a desk dock (where you want an 80% threshold) and traveling or flying (where you want a temporary 100% full charge), `batctl` (available via `yay -S batctl-tui`) is a great companion to PPD.

- [AUR package](https://aur.archlinux.org/packages/batctl-tui)
- [GitHub](https://github.com/Ooooze/batctl)

Install `batctl` from AUR:

```bash
yay -S batctl-tui
sudo batctl detect
sudo batctl status
sudo batctl
```

> [!NOTE]
> After changing your battery charging settings, make sure you see no errors. If you have an additional battery or a Bluetooth device with a battery, `batctl` automatically detects it and applies the changes to the secondary battery as well. This causes a small bug: you may see your start threshold change while the stop threshold stays at full. To verify your changes, follow the steps below.

Confirm your T14s uses the standard sysfs paths:

```bash
ls -la /sys/class/power_supply/BAT*/charge_*_threshold
```

Then verify the values:

```bash
$ cat /sys/class/power_supply/BAT0/charge_start_threshold
20
$ cat /sys/class/power_supply/BAT0/charge_stop_threshold
80
```
