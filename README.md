# arch-at-t14s

Rolling documentation of an Arch Linux installation and configuration for a Lenovo ThinkPad T14s Gen 6 (Intel).

## Goal

This repo is a personal, living reference for setting up and maintaining Arch Linux on this specific machine. The aim is to make a fresh install reproducible: install steps, package lists, and hardware-specific fixes (power management, Bluetooth, hibernation, etc.) are written down as I discover them, so I don't have to relearn or re-Google them the next time I reinstall or hit the same issue. It's documentation-first: there's no build, lint, or test tooling here, just markdown notes, config exports, and package lists.

## Repository structure

```
.
├── archinstall/                       # archinstall automation & config
│   ├── archinstall-step.md            # step-by-step archinstall walkthrough
│   ├── archinstall-remote-config.md   # loading an archinstall config from a remote URL
│   ├── configs/                       # exported archinstall JSON configs
│   │   ├── user_configuration.json
│   │   └── user_credentials.json
│   └── further-discussions/
│       ├── example-installation_summary.json
│       └── config-choices.md          # config choices & rationale for beginners
├── packages/                          # package lists
│   ├── pkglist.txt                    # base package list
│   └── aur-list.txt                   # AUR package list
├── initial-setups/                     # hardware-specific notes & fixes
│   ├── bluetooth-connection.md
│   ├── hibernation.md
│   ├── power-manegement.md            # TLP vs power-profiles-daemon
│   ├── remap-power-button.md          # power button: short press suspend, long press poweroff
│   ├── steps.md                       # (gitignored, local scratch notes)
│   └── to-do.md                       # (gitignored, local scratch notes)
├── sys-specs/
│   └── system-specs.md                # hardware/system info dumps (dmidecode, etc.)
├── ansible/                           # (reserved for future Ansible playbooks)
├── scripts/                           # (reserved for future install/setup scripts)
├── post-install-packages.md           # packages to install right after a fresh install
├── CONTRIBUTING.md                    # contribution guidelines
└── LICENSE
```

## Why ThinkPad + Arch Linux

In my college years I used several ThinkPad machines (X200, T420, T430, X1 Gen 6) because they were affordable and easy to tinker with. Windows ran too heavy on the older hardware, so I discovered Linux. Arch Linux is reliable, minimal, and one of the most widely used distros for that reason. ThinkPads in general are durable, repairable, and upgradeable machines that suit many professionals' needs as a daily driver.

Now that I mainly use a MacBook, it's time to revisit an old friend: Linux. Freedom, security, and full authority over the machine: on some ThinkPad models it's even relatively easy to remove Intel ME for extra privacy.

If you're a developer, it's worth staying close to your dev environment. I'd personally recommend running Debian and/or Arch Linux on any Linux-compatible ThinkPad (this also applies to CentOS or Fedora). I chose Arch with SwayWM for something lightweight and fast. Lastly, and this is subjective: ThinkPads tend to have the best typing experience out of the box. The X1 Gen 6, for example, has excellent travel and a smooth, tactile feel, right up there with the classic T420 keyboard. Happy typing.

Said A.

## External Resources

- [Arch Wiki: T14s Gen 6 (Intel)](https://wiki.archlinux.org/title/Lenovo_ThinkPad_T14/T14s_(Intel)_Gen_6)
- [PSREF: T14s Gen 6 (Intel) spec sheet](https://psref.lenovo.com/syspool/Sys/PDF/ThinkPad/ThinkPad_T14s_Gen_6_Intel/ThinkPad_T14s_Gen_6_Intel_Spec.pdf)
- [ThinkPad T14s Gen 6 Linux User Guide](https://download.lenovo.com/pccbbs/mobiles_pdf/t14s_gen6_c24_linux_ug_en.pdf)
- [How to Install Arch Linux (Learn Linux TV)](https://www.youtube.com/watch?v=U8uMmsJQTZM)
