# Tools used

## Required / core

| Tool | Purpose | Link |
|---|---|---|
| Hekate / Nyx | Switch bootloader, SD partitioning, Linux flashing, USB Mass Storage | https://github.com/CTCaer/hekate/releases/latest |
| Switchroot Fedora 42 KDE | Linux distribution for the Switch | https://download.switchroot.org/fedora-42/ |
| 7-Zip | Correctly extract the Fedora `.7z` archive on Windows | https://www.7-zip.org/ |
| TegraRcmGUI | Windows RCM payload injection, APX driver, Memloader/SD UMS | https://github.com/eliboa/TegraRcmGUI/releases/latest |
| USB-C data cable | PC ↔ Switch payload/data connection | — |
| microSD card | Hekate/Fedora storage | 128 GB+ recommended |

## Optional after Fedora is installed

| Tool | Purpose | Link / command |
|---|---|---|
| Switchdeck | Steam ARM64/Box64 setup for Switch L4T | https://github.com/SildurFX/Switchdeck |
| Lua | Lua programming language | `sudo dnf5 install lua` |
| LuaRocks | Lua package manager | `sudo dnf5 install luarocks` |
| Discover | Fedora/KDE GUI software manager | Preinstalled on Fedora KDE |

## Reference guides

- NH Switch Guide: https://switch.hacks.guide/
- RCM entry: https://switch.hacks.guide/user_guide/rcm/entering_rcm
- Fusée Gelée / eMMC RCM method: https://switch.hacks.guide/fusee_gelee
- Switchroot Fedora 42: https://wiki.switchroot.org/wiki/linux/l4t-fedora-installation-guide-2
- Switchroot Linux boot stack: https://wiki.switchroot.org/wiki/linux/linux-bootstack-documentation
