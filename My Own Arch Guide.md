# Doing it Properly
These are my notes on installing Arch Linux on a [Lenovo IdeaPad 5 Pro 16ARH7](https://psref.lenovo.com/WDProduct/IdeaPad/IdeaPad_5_Pro_16ARH7). This laptop has been modified slightly: The original 512GB NVMe SSD has been swapped for a [Western Digital WD_Black SN770](https://www.westerndigital.com/en-us/products/internal-drives/wd-black-sn770-nvme-ssd?sku=WDS100T3X0E).

The document will serve as a roadmap for the planned install with added notes explaining issues and solutions. Briefly, the plan is as follows:
- I will be taking notes as I go along. I will not neglect taking notes.
- I will only be using half the SSD capacity for the Arch partition table, as I plan to dual boot with Windows.
- I will use a simple split of *boot | swap | root | home*. Root & home will have LVM encryption
- The display manager will most likely be LightDM. XFCE will definitely be the desktop environment.
- I will probably use NetworkManager to get started.

## Disclaimer
This is not a guide or tutorial on how to install Arch. Do not refer to this document for advice or guidance on how to install Arch or how to troubleshoot your Arch installation process.

This is a reference document for my own use, made public for easy accessibility. Any benefit you might derive is completely incidental. Any problems derived from following these steps are to be blamed on you and you alone.

## 1. Booting into the installation image
The IdeaPad 5 Pro boot menu can be accessed with **F12**.

**F2** opens the UEFI settings menu.

**F10** opens some Lenovo tools.

Hit **12** on startup. Select the USB device to start.

### Important things to do first
Open the [official installation guide](https://wiki.archlinux.org/title/Installation_guide) and follow along.

Find your keyboard layout name: 

```
localectl list-keymaps 
```

Then, load the proper layout: 

``` 
loadkeys [layout_name]
```

Make a note of the chosen keymap for later use.

Connect to Wifi with ``` iwctl ```:
```
iwctl
station wlan0 scan
station wlan0 connect [SSID]
exit
```

Test for connectivity with ``` ping ```:

``` 
ping [website]
```

Update the system clock:

```
timedatectl 
```

## 1. Disk partitioning
IMPORTANT NOTE: Leave space for your future Windows install. About half the total volume should remain unused when creating the GPT.

Remember to double-check block devices often with ``` lsblk ```

Use *fdisk* to create a partition table: 

```
fdisk /dev/[nameofdisk]
```

You want three (2) partitions: /boot, swap and a large partition for logical volumes.

You will be using one (1) volume group containing three (3) logical volumes. It should look like this:

![gpt drawio](https://github.com/user-attachments/assets/0380ec96-1456-4037-86e8-b5e3afca5760)

The boot partition should be of the EFI type, while the storage partition should be EXT4.

### LVM encryption
Follow the [LVM on LUKS guide](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS) to set up the encrypted partition.
This guide also includes proper formatting methods for the logical volumes.

Be sure to remember this password! Are you absolutely sure you have it? Okay. Go on then.

Don't forget to also mount the boot partition when returning to the regular Installation guide.

## 2. Package installation
Check if the mirrorlist is ordered appropriately

```
vim /etc/pacman.d/mirrorlist 
```

Run pacstrap to install the base system

```
pacstrap -K /mnt base linux linux-firmware 
```

For simplicity, you will only be installing the stable Linux kernel on this setup.

At this point you should definitely install some additional packages.

```
pacman -Syu amd-ucode networkmanager lvm2 man-db man-pages texinfo vim 
```

Please remember to enable networkmanager.

```
systemctl enable NetworkManager.service 
```

With base, networking, lvm and text editing software installed, you can go ahead with configuration. The minimal install has been achieved, and additional packages can be installed later.

## 3. System configuration & finalization
We're getting there :)

This section has several EXTREMELY IMPORTANT steps. 

### IMPORTANT: Generate fstab using ``` -L ``` for "labels".
```
genfstab -L /mnt >> /mnt/etc/fstab 
```

Check the resulting /mnt/etc/fstab file, and edit it in case of errors. Refer to the install guide if in doubt

### Change root into the installed system

```
arch-chroot /mnt 
```

You're now working from the actual install.

### Timezone, keymap and hostname
You'll set the the time zone to Copenhagen.

```
ln -sf /usr/share/zoneinfo/Europe/Copenhagen /etc/localtime 
```

... And generate ``` /etc/adjtime ``` ...

```
hwclock --systohc 
```

Note: Check the install guide if any issues arise with the system clock.

Edit ``` /etc/locale.gen ``` and uncomment ``` en_DK.UTF-8 UTF-8 ``` and other needed UTF-8 locales. Generate the locales by running: 

```
locale-gen 
```

Set your console keyboard layout

``` 
vim /etc/vconsole.conf
```
Input: ```KEYMAP=thekeymapyounotedearlier ```

Set the hostname
```
vim /etc/hostname 
```
Input: ``` [host] ```

### IMPORTANT: Configuring mkinitcpio
You'll be using the default option found here: [LVM on LUKS guide](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS)

``` 
vim /etc/mkinitcpio.conf
``` 

Input: ``` HOOKS=(base udev autodetect microcode modconf kms keyboard keymap consolefont block encrypt lvm2 filesystems fsck) ```

Then generate mkinitcpio
``` 
mkinitcpio -P
```

Errors should be listed in output.

### Root password
Set your root password with 
``` 
passwd
```

Be sure to also remember this one.

### IMPORTANT: Install your boot loader
For this install you will be using [systemd-boot](https://wiki.archlinux.org/title/Systemd-boot).

Follow the instructions in the link.

### Reboot and verify
Exit chroot: 
```
exit 
```
And you're done!
``` 
reboot
```

When you've succesfully rebooted the system, you're probably itching to get started with a desktop environment and some other general purpose programs.

Please take a break. Right now. 

When you've been away from the PC for a bit, go ahead and read the [General recommendations page](https://wiki.archlinux.org/title/General_recommendations).
