# Simple actions, commands etc.
This is a document describing some normal procedures in Linux which may be easy, but can feel somewhat obscure. 

## Preparing a USB boot drive
First you need to identify the drive you will be using to create your boot drive.

In the terminal type: ``` lsblk ```

The USB drive will be named sdX, X being specific to your current situation.
Please note the sizes of the various devices to determine which one is the USB drive. The computer I'm using now uses an NVMe as its' only internal storage device, so my USB drive appears as sda.
Thus, the path to the USB drive is /dev/sda.

Next, choose a filesystem to use when formatting.

FAT32: ``` sudo mkfs.vfat /dev/[device_name] ```

NTFS: ``` sudo mkfs.ntfs /dev/[device_name] ```

exFAT: ``` sudo mkfs.exfat /dev/[device_name] ```

EXT4 ``` sudo mkfs.ext4 /dev/[device_name] ```

In my case, I need to use FAT32, so I use the command: ``` sudo mkfs.vfat /dev/sda ```

Then, I'm ready to add the ISO image to the drive.
cd to the folder where you downloaded the image to. Then use these two commands:
```
sudo cp debian-12.7.0-amd64-netinst.iso /dev/sda
sync
```

Substitute "debian-*" for your image and "sda" for your USB drive.
