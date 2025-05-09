# SETUP

## Connect to the internet

    iwctl

## Update the system clock

    timedatectl

## Partition the disks

    fdisk -l

    fdisk */dev/the_disk_to_be_partitioned*

### Example layout

    MOUNT   PARTITION                   MIN_SIZE
    /boot   /dev/efi_system_partition   1 GiB
    [SWAP] 	/dev/swap_partition         4 GiB
    / 	/dev/root_partition         23–32 GiB

## Encryption

    cryptsetup luksFormat /dev/root_partition
    cryptsetup open --type luks /dev/root_partition disk

## Format the partitions

    mkfs.ext4 /dev/mapper/disk
    mkswap /dev/swap_partition
    mkfs.fat -F 32 /dev/efi_system_partition

## Mount the file systems

    mount /dev/mapper/disk /mnt
    mount --mkdir /dev/efi_system_partition /mnt/boot
    swapon /dev/swap_partition

# Installation

## Install essential packages

    pacstrap -K /mnt base base-devel docker efibootmgr git grub hyprland intel-media-driver kitty linux-firmware linux linux-headers mesa neovim networkmanager pipewire sof-firmware tlp ttf-jetbrains-mono-nerd waybar wl-clipboard xorg-xhost zsh

# Configure the system

## Fstab

    genfstab -U -p /mnt >> /mnt/etc/fstab

## Chroot

    arch-chroot /mnt

## Time

    ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
    hwclock --systohc

## Localization

    nvim /etc/locale.gen
    locale-gen
    echo "LANG=en_US.UTF-8" > /etc/locale.conf
    echo "KEYMAP=pl" > /etc/vconsole.conf
        
## Network configuration

    echo "arch" > /etc/hostname

## Initramfs
    nvim /etc/mkinitcpio.conf
    # Add encrypt between block and filesystems
    mkinitcpio -P

## Root password

    passwd

## TLP

    systemctl enable tlp

## Add user

    useradd -m user
    passwd user

## Boot loader

    nvim /etc/default/grub
    # Add cryptdevice=/dev/root_partition:disk
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch
    grub-mkconfig -o /boot/grub/grub.cfg
