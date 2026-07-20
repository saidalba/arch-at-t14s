## Power Management: TLP vs. power-profiles-daemon (PPD)

### Why you can't use both?

TLP and `power-profiles-daemon` (PPD) are both power management tools, but **they cannot run at the same time**. Both try to control the same hardware settings, which causes conflicts and you may see warning messages in your system logs, and desktop environments like GNOME will often disable battery-related settings entirely because they detect two tools fighting for control.

### Approach 1(PPD + udev & sysfs)

To avoid conflicts, we'll use:

- **PPD**: for CPU performance modes and desktop integration (it has a native UI and works well with most desktop environments)  
- **A `udev`/`sysfs` rule**: for setting battery charge thresholds (something PPD doesn't handle on its own)

This gives you the best of both: native performance switching, custom battery limits, and no background conflicts.

### Step 1: Remove TLP

Make sure TLP is fully stopped and disabled so it can't interfere:

sudo systemctl disable \--now tlp tlp-sleep

sudo systemctl mask tlp

### Step 2: Enable PPD

sudo systemctl enable \--now power-profiles-daemon

### Step 3: Set a permanent battery charge limit

PPD doesn't manage battery charge thresholds, so we'll create a config file that sets them automatically every time the system boots.

1. Create the config file:  
     
   sudo nano /etc/tmpfiles.d/battery-thresholds.conf  
     
2. Paste in the following (this means: start charging at 75%, stop at 80%):  
     
   w /sys/class/power\_supply/BAT0/charge\_control\_start\_threshold \- \- \- \- 75  
     
   w /sys/class/power\_supply/BAT0/charge\_control\_end\_threshold \- \- \- \- 80  
     
   💡 Want different limits? Just change `75` and `80` to your preferred start/stop percentages.  
     
3. Save and exit (`Ctrl+O`, then `Enter`, then `Ctrl+X`).

### Step 4: Apply the settings immediately

You don't need to reboot: this command applies the file right away:

sudo systemd-tmpfiles \--create /etc/tmpfiles.d/battery-thresholds.conf

### Step 5: Verify everything is working

**Check PPD is active:**

powerprofilesctl

You should see an asterisk (`*`) next to your active profile (usually `balanced`).

**Check your battery threshold:**

cat /sys/class/power\_supply/BAT0/charge\_control\_end\_threshold

This should print `80` (or whatever value you set).

### Switching power profiles

**List available profiles** (usually `performance`, `balanced`, and `power-saver`):

powerprofilesctl list

**Switch to a profile:**

powerprofilesctl set power-saver

Replace `power-saver` with `balanced` or `performance` as needed.


### Approach 2(PPD + batct):

This approach is my personal choice for now. It's easier to make changes. And there is a abstraction layer. If you are someone who frequently transitions from a desk dock (where you want an 80% threshold) to traveling/flying (where you want a temporary 100% full charge), batctl (available via yay -S batctl-tui) is a great choice alongside PPD.

[AUR](https://aur.archlinux.org/packages/batctl-tui)
[Github](https://github.com/Ooooze/batctl)

### Step 1: Check TLP

Make sure TLP is not installed nor working, by default it is not but it's good to check:

```bash
[<user>@<host> ~]$ pacman -Qi tlp
error: package 'tlp' was not found
[<user>@<host> ~]$ pgrep -a tlp
```

Does your T14s have standard sysfs paths?
```bash
ls -la /sys/class/power_supply/BAT0/charge_*_threshold
```


```bash
yay -S batctl-tui
sudo batctl detect
sudo batctl status
sudo batctl
```

---