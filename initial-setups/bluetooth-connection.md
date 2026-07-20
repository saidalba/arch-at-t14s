## Set Up & Connect a Bluetooth Mouse

**Step 1: Install Bluetooth tools and start the service**

```bash
sudo pacman -S bluez bluez-utils
sudo systemctl enable --now bluetooth
```

**Step 2: Put your mouse into pairing mode**

Press and hold the button on the bottom of your mouse for about 3 seconds, until the LED starts flashing rapidly.

**Step 3: Open the Bluetooth control tool**

```bash
bluetoothctl
```

Inside the `bluetoothctl` prompt, run:

```bash
power on
default-agent
agent on
```

Fix for Bluetooth Low Energy (BLE) scanning (if the device is not found):

```bash
menu scan
transport le
back
scan on

# Wait for "Device XX:XX:XX:XX:XX:XX MX Anywhere 3S" to appear
# After some time (when you see your target Bluetooth device):
scan off
```

You'll see a list of nearby devices appear. Look for your mouse's name (e.g. "MX Master 4") and copy its MAC address, it looks like `XX:XX:XX:XX:XX:XX`.

**Step 4: Pair, trust, and connect**

Replace `XX:XX:XX:XX:XX:XX` with your mouse's actual MAC address:

```bash
pair XX:XX:XX:XX:XX:XX
trust XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
```

Then type `exit` to leave `bluetoothctl`. Your mouse should now be connected and ready to use.

**Step 5: Verifying Connection Status**

```bash
devices Connected
info XX:XX:XX:XX:XX:XX
```

A successful connection shows `Paired: yes`, `Trusted: yes`, `Connected: yes`, and outputs the current battery percentage.

**Common Issue: Missing Kernel Module (`hid_logitech_hidpp`)**

If your mouse pairs and connects successfully but still doesn't move, it may be due to a missing kernel module. The MX Anywhere 3S and MX Master 4 rely on the standard Logitech HID++ kernel driver. If this module isn't loaded, the system recognizes the device but won't process its movements.

```bash
sudo modprobe hid_logitech_hidpp
```

Test your mouse. If it starts moving, make the fix permanent so it loads automatically on boot:

```bash
echo "hid_logitech_hidpp" | sudo tee /etc/modules-load.d/logitech.conf
```

Now reboot your system and it should be working as expected.
