## Install and Configure chezmoi for Dotfile Management

`chezmoi` manages dotfiles across machines from a single source directory, kept as its own git repo, and applies them to your home directory.

**Step 1: Install chezmoi**

```bash
sudo pacman -S chezmoi
```

**Step 2: Initialize chezmoi**

```bash
chezmoi init
```

This creates the source directory at `~/.local/share/chezmoi`, which chezmoi manages as its own git repository.

**Step 3: Add dotfiles to be managed**

```bash
chezmoi add ~/.bashrc
chezmoi add ~/.config/nvim/init.lua
```

Each `add` copies the target file into the source directory so chezmoi can track and template it.

**Step 4: Review and apply changes**

Edit a tracked file through chezmoi (or edit it directly in `~/.local/share/chezmoi`), then preview and apply:

```bash
chezmoi edit ~/.bashrc
chezmoi diff
chezmoi apply
```

**Step 5: Push the source directory to a remote**

```bash
cd ~/.local/share/chezmoi
git remote add origin https://github.com/<user>/dotfiles.git
git push -u origin master
```

`chezmoi init` starts the source repo on a `master` branch by default, not `main`. Push as `master` unless you've explicitly renamed the branch locally.


## Managing SSH and API Keys

Secrets need different handling than plain dotfiles: they should never land in the source repo as plaintext, even if that repo is private. chezmoi supports two approaches, and they suit different key types.

**Option 1: age encryption (for static secrets like an SSH private key)**

```bash
chezmoi add --encrypt ~/.ssh/id_ed25519
```

chezmoi encrypts the file with `age` before it ever touches the git repo, storing it as `encrypted_private_dot_ssh/id_ed25519.age`. It's decrypted automatically on `chezmoi apply` using a local age identity file.

**Why:** works fully offline, no third-party CLI dependency. The tradeoff is the repo carries encrypted blobs, and the age identity file itself must be kept safe and never committed.

**Option 2: password-manager templating (for API keys and tokens)**

Instead of adding the real file, create a template that looks the secret up at apply-time, for example `~/.ssh/id_ed25519.tmpl` or a template for an API token, using a function like:

```
{{ onepasswordRead "op://Personal/GitHub Token/credential" }}
```

(or `{{ bitwardenFields ... }}`, or a `pass`/`gopass` lookup, depending on which manager you use).

**Why:** the source repo only stores the lookup query, never the secret itself, so nothing sensitive is committed even in encrypted form. The tradeoff is every machine needs that manager's CLI installed and authenticated before `chezmoi apply` will succeed.

**Recommendation:** age for the SSH private key (no external dependency, works offline), password-manager templating for API keys (no encrypted blob to manage, and tokens are often already stored in a manager and rotated periodically).

