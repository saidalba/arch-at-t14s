## Archinstall Installation Steps

> [!NOTE]
> If you want to copy your archinstall config from aremote URL see:  [archinstall-remote-config.md](archinstall/archinstall-remote-config.md)

After burning your archiso media, boot your system and follow these steps:

**Step 1: Test your network connection**

Check if your ethernet connection is up:

```bash
root@archiso ~$ ip addr show
```

If your ethernet interface has an IP address assigned, verify connectivity by pinging Google:

```bash
root@archiso ~$ ping -c 5 8.8.8.8
```

If this returns a successful response 5 times, you're good to go.

**Step 2: Connect to Wi-Fi (if no ethernet)**

If you don't have an ethernet connection, use `iwctl` to connect to Wi-Fi instead:

```bash
root@archiso ~$ iwctl
[iwd] device list
[iwd] station wlan0 get-networks
[iwd] station wlan0 connect <target-wifi-ssid>
Passphrase: ********
[iwd] exit
```

Confirm you now have an IP address:

```bash
root@archiso ~$ ip addr show
```

**Step 3: Run archinstall**

```bash
root@archiso ~$ archinstall
```

Now you're in the archinstall installation daemon. Set your config or import it from USB media.

> [!NOTE]
> If you want to open up a new terminal screen without losing your current `archinstall` session, you can do so: `Ctrl` + `Alt` + `F2` and you go back to your `archinstall` session with `Ctrl` + `Alt` + `F2`
