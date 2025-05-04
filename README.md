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
    / 	    /dev/root_partition         23–32 GiB

## Format the partitions

    mkfs.ext4 /dev/root_partition
    mkswap /dev/swap_partition
    mkfs.fat -F 32 /dev/efi_system_partition

## Mount the file systems

    mount /dev/root_partition /mnt
    mount --mkdir /dev/efi_system_partition /mnt/boot
    swapon /dev/swap_partition

# Installation

## Install essential packages

    pacstrap -K /mnt base

# Configure the system

## Fstab

    genfstab -U /mnt >> /mnt/etc/fstab

## Chroot

    arch-chroot /mnt

## Time

    ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
    hwclock --systohc

## Localization

    locale-gen
    echo "LANG=en_US.UTF-8" > /etc/locale.conf
    echo "KEYMAP=pl" > /etc/vconsole.conf
        
## Network configuration

    echo "arch" > /etc/hostname

## Initramfs

    mkinitcpio -P

## Root password

    passwd

## Boot loader

    



