### List of post-install packages

This file contains list of packages to be installed right after fresh install.

#!/bin/bash

echo "Updating system repositories..."
sudo pacman -Syu --noconfirm

echo "Installing essential packages..."
sudo pacman -S --needed --noconfirm \
  firefox \
  neovim \
  micro \
  zed \ 
  acpi \
  dmidecode \
  fastfetch \
  cmatrix \
  git \
  base-devel \
  usbutils



# install yay
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd .. 
rm -rf yay


# install claude-cli from AUR
yay -S claude-code-stable


echo "All packages installed successfully!"