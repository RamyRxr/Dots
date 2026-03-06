# 🚀 Arch Linux Master Installation & Configuration Guide
[cite_start]**Author:** Ramy 
[cite_start]**System:** Arch Linux + Hyprland (Caelestia Theme) 

---

## 🛠 Phase 1: Pre-Installation (Live ISO)
### 1. Network Connectivity
* [cite_start]Set the terminal font for better readability: `setfont ter-132n`.
* [cite_start]Connect to Wi-Fi using `iwctl`: 
  ```bash
  iwctl
  device list
  station wlan0 get-networks
  station wlan0 connect <network_name>
  exit
Verify connection: ping google.com -c 4.

2. Manual Partitioning (NVMe)
Identify your drive: lsblk or cfdisk /dev/nvme0n1.

Partition Layout:


1G -> EFI System Partition.


119G -> Linux Filesystem (Root).


Format Partitions: 

Bash
mkfs.fat -F32 /dev/nvme0n1p3  # (Verify partition index)
mkfs.ext4 /dev/nvme0n1p4
🛰 Phase 2: Core Installation (archinstall)
Update the keyring if necessary: sudo pacman -Sy archlinux-keyring archinstall.


Run Installer: archinstall.

Disk Configuration:

Select "Manual Partitioning" -> Choose /dev/nvme0n1.

Assign Mountpoints: 1G to /boot and 119G to /.

Critical Settings:


Bootloader: GRUB.


Audio: Pipewire.


Network: NetworkManager.


Additional Packages: nano, os-prober, git, fuse3.

Post-Install GRUB Setup (Chroot)
Before rebooting, enter the chroot environment to finish the bootloader: 

Bash
sudo pacman -S efibootmgr grub mtools dosfstools
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
exit
reboot
🎨 Phase 3: UI Installation (JaKooLit Baseline)

Connect Wi-Fi: nmcli d wifi connect <SSID> --ask.


Clone JaKooLit Repository: 

Bash
git clone [https://github.com/JaKooLit/Arch-Hyprland.git](https://github.com/JaKooLit/Arch-Hyprland.git)
cd Arch-Hyprland
./install.sh

Selection: Choose yay as the AUR helper and skip ROG packages if using NVIDIA.

🌪 Phase 4: Transitioning to Caelestia
To avoid theme conflicts, back up and remove the JaKooLit configurations before installing Caelestia.

1. The Clean Sweep
Move existing configs to a backup folder: 

Bash
mkdir ~/jakoolit_backup
mv ~/.config/hypr ~/jakoolit_backup/
mv ~/.config/waybar ~/jakoolit_backup/
mv ~/.config/rofi ~/jakoolit_backup/
mv ~/.config/kitty ~/jakoolit_backup/
mv ~/.config/dunst ~/jakoolit_backup/
2. Install Caelestia

Install Dependencies: yay -S foot zen-browser-bin quickshell-git caelestia-cli-git dart-sass app2unit inotify-tools.


Deployment: 

Bash
git clone [https://github.com/caelestia-dots/caelestia.git](https://github.com/caelestia-dots/caelestia.git) ~/.local/share/caelestia
cd ~/.local/share/caelestia
./install.fish
🔧 Phase 5: Custom Fixes & Power Tweaks
1. Dual Monitor Alignment

File: ~/.config/hypr/hyprland.conf.

Fix the monitor coordinates (Left vs. Right): 

Bash
# monitor = name, resolution, position, scale
monitor = HDMI-A-1, 1920x1080, 0x0, 1
monitor = DP-1, 1920x1080, 1920x0, 1
2. Super Key Launcher Fix

File: ~/.config/hypr/hyprland/keybinds.conf.

Add this to ensure the Windows key alone triggers the menu: 

Bash
bindr = SUPER, Super_L, exec, pkill fuzzel || caelestia launcher
3. Draggable Title Bars (Hyprbars Plugin)
Allows moving windows with just the mouse (no Super key).


Install Tools: sudo pacman -S --needed cmake cpio pkgconf git gcc.


Enable Plugin: 

Bash
hyprpm update
hyprpm add [https://github.com/hyprwm/hyprland-plugins](https://github.com/hyprwm/hyprland-plugins)
hyprpm enable hyprbars
Configuration:

Add to ~/.config/hypr/hyprland/decoration.conf: 

Bash
plugin {
    hyprbars {
        bar_height = 25
        bar_color = rgba(1a1a1aee)
        hyprbars-button = rgb(ff5f59), 15, 󰖭, hyprctl dispatch killactive
        hyprbars-button = rgb(ffbd2e), 15, 󰖯, hyprctl dispatch togglefloating
    }
}
4. Default Floating Rules

File: ~/.config/hypr/hyprland/rules.conf.

Make specific apps draggable by default: 

Bash
windowrule = float, ^(kitty)$
windowrule = float, ^(nemo)$