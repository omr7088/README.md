# README.md
Arch Linux Installation Process Documentation

## Overview

**Date of Documentation**: October 27, 2024  
**Installation Environment**: VirtualBox Virtual Machine  
**Base System Specifications**:
- RAM: 2GB
- Storage: 20GB
- CPU: 2 cores
- Network: NAT
- Boot Mode: UEFI

## 1. Pre-Installation Process

### 1.1 Virtual Machine Setup
1. Created new VM in VirtualBox
   - Name: Arch Linux
   - Type: Linux
   - Version: Arch Linux (64-bit)
2. Configured hardware resources:
   ```
   Memory: 2000 MB
   CPU: 2 cores
   ```
3. Created virtual hard disk:
   ```
   Type: VDI
   Allocation: Dynamic
   Size: 20 GB
   ```

### 1.2 Boot Environment Verification
```
# Verified UEFI boot mode
ls /sys/firmware/efi/efivars
# Result: Directory exists, confirming UEFI mode

# Checked internet connectivity
ping -c 3 archlinux.org
# Result: Successful ping responses

# Updated system clock
timedatectl set-ntp true
# Result: Clock synchronized
```

## 2. Disk Partitioning and Formatting

### 2.1 Partition Creation
```
# Listed available disks
fdisk -l
# Result: Identified target disk as /dev/sda

# Created partitions using fdisk
fdisk /dev/sda

# Commands used:
# g - Created new GPT partition table
# n - Created new partition (EFI)
#   Partition 1: +500M
# n - Created new partition (Root)
#   Partition 2: Remaining space
# t - Changed partition type for EFI to 1 (EFI System)
# w - Wrote changes to disk
```

 2.2 Formatting Partitions
```
# Formatted EFI partition
mkfs.fat -F32 /dev/sda1
# Result: FAT32 filesystem created

# Formatted root partition
mkfs.ext4 /dev/sda2
# Result: Ext4 filesystem created

# Mounted partitions
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

3. Base System Installation
3.1 Package Installation
```
# Installed base system
pacstrap -K /mnt base linux linux-firmware
# Result: Base system installed successfully

# Installed additional essential packages
pacstrap -K /mnt base-devel networkmanager vim sudo
# Result: Additional packages installed
```

### 3.2 System Configuration
```
# Generated fstab
genfstab -U /mnt >> /mnt/etc/fstab
# Result: Filesystem table generated

# Entered chroot environment
arch-chroot /mnt

# Set timezone
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc

# Configured locale
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
``dd
## 4. Custom Requirements Implementation

### 4.1 Desktop Environment (LXQT)
```
# Installed X server
pacman -S xorg xorg-server
# Result: X server installed successfully

# Installed LXQT and display manager
pacman -S lxqt xdg-utils ttf-dejavu sddm
# Result: LXQT environment installed

# Enabled display manager for GUI boot
systemctl enable sddm
# Result: SDDM enabled for next boot
```

### 4.2 User Account Creation
```
# Created user accounts with sudo permissions
useradd -m -G wheel justin
useradd -m -G wheel codi

# Set initial passwords
passwd justin  # Set to: GraceHopper1906
passwd codi    # Set to: GraceHopper1906

# Configured password expiration
passwd -e justin
passwd -e codi

# Configured sudo access
EDITOR=vim visudo
# Uncommented: %wheel ALL=(ALL:ALL) ALL
```

### 4.3 Shell Installation (ZSH)
```
# Installed ZSH
pacman -S zsh
# Result: ZSH installed successfully

# Set as default shell for users
chsh -s /bin/zsh justin
chsh -s /bin/zsh codi

# Created ZSH configuration
cat > /home/justin/.zshrc << 'EOL'
# Terminal settings
export TERM="xterm-256color"

# Color aliases
alias ls='ls --color=auto'
alias grep='grep --color=auto'

# Useful aliases
alias ll='ls -la'
alias update='sudo pacman -Syu'
alias install='sudo pacman -S'
alias remove='sudo pacman -R'
alias ..='cd ..'

# Enable colors
autoload -U colors && colors
PS1="%{$fg[green]%}%n%{$reset_color%}@%{$fg[blue]%}%m %{$fg[yellow]%}%~ %{$reset_color%}%% "
EOL

# Copied configuration to other user
cp /home/justin/.zshrc /home/codi/.zshrc

# Set proper ownership
chown justin:justin /home/justin/.zshrc
chown codi:codi /home/codi/.zshrc
```

### 4.4 SSH Installation
```
# Installed SSH
pacman -S openssh
# Result: SSH installed successfully

# Enabled SSH service
systemctl enable sshd
# Result: SSH service enabled for next boot
```

## 5. Issues Encountered and Resolutions

### 5.1 Issue: Visudo Editor Not Found
**Problem**: When running `visudo`, received error "visudo: no editor found (editor = nano)"
**Solution**: 
```
pacman -S nano
EDITOR=nano visudo
```

### 5.2 Issue: Terminal Colors Not Working
**Problem**: Color settings in .zshrc not taking effect
**Solution**: Added necessary terminal package and reloaded configuration
```
pacman -S zsh-syntax-highlighting
source ~/.zshrc
```

## 6. Post-Installation Verification

### 6.1 System Checks
```
# Verified services status
systemctl status sddm
systemctl status sshd
systemctl status NetworkManager
# Result: All services running properly

```

### 6.2 Desktop Environment
-SDDM loads correctly
-LXQT starts properly
-Basic functionality works as expected

## 7. Lessons Learned and Tips

### 7.1 Important Tips
1. Always verify disk partitioning before formatting
2. Document all passwords and configuration changes
3. Test user accounts before exiting installation
4. Verify network connectivity after each major step

### 7.2 Time-Saving Practices
1. Keep package installation commands in a single line
2. Test critical services before rebooting

## 8. Additional Notes

### 8.1 Optional Improvements
1. Installed additional packages:
```
pacman -S firefox  # Web browser
pacman -S network-manager-applet  # Network GUI
pacman -S qterminal  # Terminal emulator
```


## 9. Reference Materials Used
1. Arch Linux Installation Guide
