# 🚀 Arch Linux Master Installation & Configuration Guide

**Author:** Ramy  
**System:** Arch Linux + Hyprland (Caelestia Theme)

---

## 📋 Table of Contents

1. [Phase 1: Pre-Installation (Live ISO)](#phase-1-pre-installation-live-iso)
2. [Phase 2: Core Installation (archinstall)](#phase-2-core-installation-archinstall)
3. [Phase 3: UI Installation (JaKooLit Baseline)](#phase-3-ui-installation-jakoolit-baseline)
4. [Phase 4: Transitioning to Caelestia](#phase-4-transitioning-to-caelestia)
5. [Phase 5: Custom Fixes & Power Tweaks](#phase-5-custom-fixes--power-tweaks)

---

## 🛠 Phase 1: Pre-Installation (Live ISO)

### 1.1 Network Connectivity

Set the terminal font for better readability:
```bash
setfont ter-132n
```

Connect to Wi-Fi using `iwctl`:
```bash
iwctl
device list
station wlan0 get-networks
station wlan0 connect <network_name>
exit
```

Verify connection:
```bash
ping google.com -c 4
```

---

### 1.2 Manual Partitioning (NVMe)

Identify your drive:
```bash
lsblk
# or
cfdisk /dev/nvme0n1
```

**Partition Layout:**

| Size  | Type                  | Purpose        |
|-------|-----------------------|----------------|
| 1 GB  | EFI System Partition  | `/boot`        |
| 119 GB | Linux Filesystem     | `/` (Root)     |

Format the partitions:
```bash
mkfs.fat -F32 /dev/nvme0n1p3   # Verify partition index first
mkfs.ext4 /dev/nvme0n1p4
```

---

## 🛰 Phase 2: Core Installation (archinstall)

### 2.1 Run the Installer

Update the keyring if necessary:
```bash
sudo pacman -Sy archlinux-keyring archinstall
```

Launch the installer:
```bash
archinstall
```

---

### 2.2 Disk Configuration

- Select **"Manual Partitioning"** → Choose `/dev/nvme0n1`
- Assign mountpoints:
  - `1 GB` → `/boot`
  - `119 GB` → `/`

---

### 2.3 Critical Settings

| Setting             | Value                                      |
|---------------------|--------------------------------------------|
| Bootloader          | GRUB                                       |
| Audio               | Pipewire                                   |
| Network             | NetworkManager                             |
| Additional Packages | `nano`, `os-prober`, `git`, `fuse3`        |

---

### 2.4 Post-Install GRUB Setup (Chroot)

Before rebooting, enter the chroot environment to finish the bootloader:
```bash
sudo pacman -S efibootmgr grub mtools dosfstools
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
exit
reboot
```

---

## 🎨 Phase 3: UI Installation (JaKooLit Baseline)

Connect to Wi-Fi after reboot:
```bash
nmcli d wifi connect <SSID> --ask
```

Clone and run the JaKooLit Hyprland installer:
```bash
git clone https://github.com/JaKooLit/Arch-Hyprland.git
cd Arch-Hyprland
./install.sh
```

> **Note:** Choose `yay` as the AUR helper. Skip ROG packages if using NVIDIA.

---

## 🌪 Phase 4: Transitioning to Caelestia

To avoid theme conflicts, back up and remove JaKooLit configurations before installing Caelestia.

### 4.1 Back Up JaKooLit Configs

```bash
mkdir ~/jakoolit_backup
mv ~/.config/hypr    ~/jakoolit_backup/
mv ~/.config/waybar  ~/jakoolit_backup/
mv ~/.config/rofi    ~/jakoolit_backup/
mv ~/.config/kitty   ~/jakoolit_backup/
mv ~/.config/dunst   ~/jakoolit_backup/
```

---

### 4.2 Install Caelestia

Install dependencies:
```bash
yay -S foot zen-browser-bin quickshell-git caelestia-cli-git dart-sass app2unit inotify-tools
```

Clone and deploy:
```bash
git clone https://github.com/caelestia-dots/caelestia.git ~/.local/share/caelestia
cd ~/.local/share/caelestia
./install.fish
```

---

## 🔧 Phase 5: Custom Fixes & Power Tweaks

### 5.1 Dual Monitor Alignment

**File:** `~/.config/hypr/hyprland.conf`

Fix monitor coordinates (Left vs. Right):
```bash
# monitor = name, resolution, position, scale
monitor = HDMI-A-1, 1920x1080, 0x0, 1
monitor = DP-1,     1920x1080, 1920x0, 1
```

---

### 5.2 Super Key Launcher Fix

**File:** `~/.config/hypr/hyprland/keybinds.conf`

Add this to ensure the Windows key alone triggers the launcher menu:
```bash
bindr = SUPER, Super_L, exec, pkill fuzzel || caelestia launcher
```

---

### 5.3 Draggable Title Bars (Hyprbars Plugin)

Allows moving windows with just the mouse (no Super key required).

Install build dependencies:
```bash
sudo pacman -S --needed cmake cpio pkgconf git gcc
```

Enable the plugin:
```bash
hyprpm update
hyprpm add https://github.com/hyprwm/hyprland-plugins
hyprpm enable hyprbars
```

Add to `~/.config/hypr/hyprland/decoration.conf`:
```bash
plugin {
    hyprbars {
        bar_height = 25
        bar_color  = rgba(1a1a1aee)
        hyprbars-button = rgb(ff5f59), 15, 󰖭, hyprctl dispatch killactive
        hyprbars-button = rgb(ffbd2e), 15, 󰖯, hyprctl dispatch togglefloating
    }
}
```

---

### 5.4 Default Floating Window Rules

**File:** `~/.config/hypr/hyprland/rules.conf`

Make specific apps float (and therefore draggable) by default:
```bash
windowrule = float, ^(kitty)$
windowrule = float, ^(nemo)$
```

---

*End of Guide*
