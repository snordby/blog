# Doing it Properly
These are my notes on installing Arch Linux on a [Lenovo IdeaPad 5 Pro 16ARH7](https://psref.lenovo.com/WDProduct/IdeaPad/IdeaPad_5_Pro_16ARH7). This laptop has been modified slightly: The original 512GB NVMe SSD has been swapped for a [Western Digital WD_Black SN770](https://www.westerndigital.com/en-us/products/internal-drives/wd-black-sn770-nvme-ssd?sku=WDS100T3X0E).

The document will serve as a roadmap for the planned install with added notes explaining issues and solutions. Briefly, the plan is as follows:
- I will be taking notes as I go along. I will not neglect taking notes.
- I will only be using half the SSD capacity for the Arch partition table, as I plan to dual boot with Windows.
- I will use a simple split of *boot | swap | root | home*. Root & home will have LVM encryption
- The display manager will most likely be LightDM. XFCE will definitely be the desktop environment.
- I will probably use NetworkManager to get started.
