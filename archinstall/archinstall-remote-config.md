## Importing an Archinstall Config from a Remote URL

`archinstall` can load its configuration directly from a URL instead of a local file, useful when you're booting a fresh archiso and don't want to manually copy config files onto the install media. This repo's config files can be pulled straight from GitHub.

**Step 1: Get networking up**

Follow [archinstall-step.md](archinstall-step.md) to get ethernet or Wi-Fi working in the archiso environment before continuing.

**Step 2: Locate the raw config URLs**

The config files live in [`archinstall/configs/`](configs/) in this repo. Use the raw GitHub URLs, not the regular `github.com` page URLs:

```bash
https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_configuration.json
https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_credentials.json
```

**Step 3: Run archinstall with `--config` and `--creds`**

`archinstall` accepts a URL directly for both the `--config` and `--creds` arguments:

```bash
root@archiso ~$ archinstall --config https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_configuration.json \
  --creds https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_credentials.json
```

This launches archinstall pre-filled with the settings from both files. Review each screen and confirm before the install starts. Nothing is applied until you reach the final confirmation step.

**Step 4: (Optional) Fully unattended install**

Add `--silent` to skip the review screens and install immediately using the loaded config, with no further prompts:

```bash
root@archiso ~$ archinstall --config https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_configuration.json \
  --creds https://raw.githubusercontent.com/saidalba/arch-at-t14s/main/archinstall/configs/user_credentials.json \
  --silent
```

Only use `--silent` once you've verified the config produces the disk layout and settings you expect: it will wipe the target disk without asking for confirmation (see `disk_config` in `user_configuration.json`).

**Note on personal values in these configs**

The password fields in `user_credentials.json` are placeholders (`********`), not real hashes, and `username` (in `user_credentials.json`) and `hostname` (in `user_configuration.json`) are set to `<redacted>`. Fill these in with your own values before using the configs:

```bash
mkpasswd -m yescrypt
```

Replace `root_enc_password` and each user's `enc_password` with the resulting hash, set `username` to your desired login name, and set `hostname` to your desired machine name, before running `archinstall --creds`.
