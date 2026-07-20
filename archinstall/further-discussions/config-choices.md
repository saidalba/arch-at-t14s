# Archinstall Configuration Choices

Arch Linux and `archinstall` are designed to be minimal, so you're free to configure the system however you like. That's great once you know what you want, but if you're new, the sheer number of choices (filesystem, bootloader, network manager, window manager, and so on) can turn into decision fatigue fast.

This page lists the choices I made for this machine and, more importantly, *why*, so you can reuse them, or use the reasoning to make your own call.

> [!NOTE]
> Prefer raw config? See [example-installation_summary.json](example-installation_summary.json) for the same choices in `archinstall`'s JSON format.

## Quick reference

| Setting | Choice |
|---|---|
| Bootloader | systemd-boot |
| Unified Kernel Images (UKI) | Enabled |
| Disk layout | Single partition, no manual partitioning |
| Filesystem | ext4 |
| Disk encryption | Off (personal preference) |
| Swap | zRAM, zstd compression |
| Profile | Sway (Wayland) |
| Seat/session access | polkit |
| Drivers | Open source only |
| Greeter | ly |
| Audio | pipewire |
| Firewall | ufw (or firewalld, see below) |
| Network manager | iwd, or NetworkManager with iwd backend |
| Power management | power-profiles-daemon (or tuned, see below) |
| Bluetooth & printing | Enabled |

Details and reasoning for each are below.

## Bootloader: systemd-boot

**Why:** Simpler and leaner than GRUB, with fewer moving parts and no extra dependencies to maintain. In my own testing on the T14s, it also boots noticeably faster. GRUB is still a fine choice if you need its extra features (e.g. chainloading exotic setups), but I don't need them here.

## Unified Kernel Images (UKI): Enabled

**Why:** Bundling the kernel, initramfs, and cmdline into a single signed EFI binary hardens Secure Boot (there's no separate, unsigned initramfs for an attacker to tamper with) and shaves a little time off boot, since the bootloader has one image to load instead of several.

## Disk configuration: single partition, no manual layout

I recommend keeping the disk as one logical unit. Don't split out a separate `/home` partition unless you have a specific reason to.

**Why:** A single standalone volume makes setting up hibernation dramatically simpler. A separate `/home` (or other) partition adds complexity to swap/resume configuration for little practical benefit on a single-user laptop. See the [hibernation guide](../../initial-setups/hibernation.md) for the full setup.

## Filesystem: ext4

**Why:** Same driver as above, hibernation is much easier to get working reliably on ext4 than on `btrfs`. If snapshots, transparent compression, or other `btrfs` features matter more to you than hibernation, `btrfs` is a solid alternative; just expect a few extra steps to make resume-from-hibernate work.

## Disk encryption: off

This one's a personal call: encrypt if you need it.

**Why:** I prioritize fast, friction-free reboots (no extra password prompt or greeter step) over the security benefit of full-disk encryption. If your device could end up lost, stolen, or handled by someone else, encryption is worth the small daily cost.

## Swap: zRAM (zstd compression)

**Why:** Compressed RAM-backed swap gives you a safety net against OOM situations without touching the disk. The compression algorithm choice isn't critical: `zstd` is a good default, and it's easy to change later once the system is up and running if you find a reason to.

## Profile: Sway (Wayland) via polkit *or* seatd

I use Sway as my window manager, an i3-like tiling compositor for Wayland, and pair it with `polkit` for privileged operations.

**Why:** Sway can run with either `polkit` or `seatd` handling privileged operations, but `polkit` is the more common pairing and plays nicer with the wider desktop-app ecosystem (GUI password prompts, network manager applets, etc.). `seatd` is the leaner option if you want minimal resource usage and don't need those conveniences. I'm considering it for the future if I switch to a suckless-style setup like `dwl`, where a minimal seat manager fits the philosophy. Note this isn't a one-way door: you can swap one for the other later if your needs change.

## Drivers: open source only

**Why:** This machine has no proprietary GPU (no Nvidia), so there's no proprietary driver to install in the first place: open source drivers cover everything.

## Greeter: ly *or* gdm/sddm

**Why:** `ly` is a minimal, ncurses-based TTY greeter: small footprint, starts fast, and does exactly one job well without pulling in a graphical stack. Heavier greeters like `gdm` or `sddm` are worth it if you want a themeable graphical login screen and don't mind the extra resource cost.

## Audio: pipewire *or* pulseaudio

**Why:** `pipewire` is the modern replacement for `pulseaudio` (and JACK), with better low-latency handling and a single daemon for both audio and video streams instead of juggling separate systems.

## Firewall: ufw *or* firewalld

**Why:** Pick based on how you use the machine. `ufw` is simpler to reason about for a small, static set of rules, good if your network context rarely changes. `firewalld` is better if you move between networks often (home, office, public Wi-Fi), since it supports switching *zones* with different trust levels on the fly instead of editing rules by hand.

## Network manager: iwd, *or* NetworkManager with iwd backend

I use both, depending on the machine.

**Why:** `iwd` is developed by Intel and pairs natively with this laptop's Intel Wi-Fi card, with a lower resource footprint than the full NetworkManager stack. `NetworkManager` (configured to use `iwd` as its backend) is worth it when you want its extra conveniences (GUI applets, VPN plugins, connection profiles) without giving up `iwd`'s native driver support.

## Power management: power-profiles-daemon (PPD) *or* tuned

I use `power-profiles-daemon` (PPD) since it works well with newer CPUs and suits the needs of most modern laptops out of the box.

**Why:** PPD is the simpler, desktop-integrated option, and it comes with a native UI that most desktop environments already understand. See the [power management notes](../../initial-setups/power-manegement.md) for the full setup, including pairing PPD with a `udev` rule for battery charge thresholds. `tuned`, by contrast, is a robust, highly customizable daemon originating from Red Hat, capable of deep system tuning across CPU, disk, and network. It's overkill for most day-to-day users, but worth considering if you need fine-grained, per-workload profiles.

## Bluetooth and printing: enabled

**Why:** Both get used constantly on a laptop, a wireless mouse or headset, and printing/scanning at home or the office, so there's little reason to leave them disabled. See the [Bluetooth setup notes](../../initial-setups/bluetooth-connection.md) for pairing a specific device.

