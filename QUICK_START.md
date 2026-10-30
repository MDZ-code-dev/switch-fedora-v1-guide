# Quick start checklist

This is the short version. Read the full README before doing the eMMC/no-jig method.

1. Confirm the Switch is an **unpatched V1**.
2. Download Hekate, Fedora 42 Switchroot, TegraRcmGUI and 7-Zip.
3. Enter RCM.
   - Standard: normal NH RCM method.
   - No-jig route used here: safely open the Switch and unseat the removable eMMC while powered off/battery disconnected.
4. Inject Hekate.
5. Copy the full Hekate `bootloader` folder to microSD root.
6. Boot Hekate/Nyx.
7. Back up the whole microSD to the PC.
8. Hekate → Tools → Partition SD Card.
9. Allocate Linux space (example: 64 GiB) and partition.
10. Mount the new FAT32 partition over USB.
11. With **7-Zip File Manager**, extract Fedora 42 directly to SD root.
12. Safely eject the SD.
13. Hekate → Partition SD Card → Flash Linux.
14. Hekate → More Configs → L4T Fedora.
15. Complete Fedora initial user setup.
16. Update Fedora 42 normally; **do not distro-upgrade to Fedora 43**.
17. Optional: Hekate Options → Auto Boot → L4T Fedora → Boot Time Delay 0.
