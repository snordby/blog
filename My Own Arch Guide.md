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
Find your keyboard layout with the command: ``` localectl list-keymaps ```

Then load the proper layout: ``` loadkeys [layout_name] ```

Connect to Wifi with *iwctl*
```
iwctl
station wlan0 scan
station wlan0 connect [SSID]
exit
```

Test for connectivity with ``` ping [website] ```

Update the system clock with ``` timedatectl ```

## Disk partitioning
IMPORTANT NOTE: Leave space for your future Windows install. About half the total volume should remain unused when creating the GPT.

Remember to double-check block devices often: ``` lsblk ```

Use *fdisk* to create a partition table: ``` fdisk /dev/nameofdisk ```

You want three (2) partitions: /boot, swap and a large partition for logical volumes.

You will be using one (1) volume group containing three (3) logical volumes. It should look like this:

![gpt drawio](https://github.com/user-attachments/assets/0380ec96-1456-4037-86e8-b5e3afca5760)


