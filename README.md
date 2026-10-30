# Nintendo Switch V1 → Fedora 42 Linux Guide

A simple guide for installing **Switchroot Fedora 42 KDE** on an **unpatched Nintendo Switch V1 (2017 / Erista)**.

This repo documents the setup I actually used, including an **advanced no-jig method** that forces RCM by temporarily removing the Switch's socketed eMMC module.

> [!WARNING]
> Opening the Switch and removing the eMMC is **not the beginner method**. You can strip screws, damage ribbon cables/connectors, short the motherboard, or damage the eMMC socket. The normal RCM jig method is safer. If you use the no-jig method, power off, unplug USB, and disconnect the battery before touching internal hardware.

## What this installs

- Hekate / Nyx bootloader
- Switchroot **Fedora 42 KDE**
- A dedicated Linux EXT4 partition on the microSD
- Optional Hekate auto-boot into Fedora
- Optional Steam through Switchdeck
- Optional Lua + LuaRocks

## Important compatibility notes

- This guide is mainly for an **unpatched V1 Switch** vulnerable to Fusée Gelée.
- Patched V1, V2, Lite and OLED systems need a different entry method (normally a modchip).
- **Do not upgrade Fedora 42 to Fedora 43**. Switchroot warns that major distro upgrades break its configuration.
- Hekate is still required to boot L4T Linux. Hekate can auto-launch Fedora, but an unpatched V1 without a modchip still needs a payload after a complete shutdown.

Official references:

- NH Switch Guide: https://switch.hacks.guide/
- RCM/Fusée Gelée: https://switch.hacks.guide/user_guide/rcm/
- Fusée Gelée disclosure / eMMC RCM method: https://switch.hacks.guide/fusee_gelee
- Switchroot Fedora 42 guide: https://wiki.switchroot.org/wiki/linux/l4t-fedora-installation-guide-2
- Switchroot Fedora downloads: https://download.switchroot.org/fedora-42/

---

## 1. What you need

### Hardware

- Unpatched Nintendo Switch V1
- microSD card — **128 GB+ recommended**
- USB-C **data** cable
- Windows PC/laptop
- Correct screwdrivers if using the no-jig/eMMC method

### Windows software

1. **7-Zip**  
   https://www.7-zip.org/

2. **Hekate / Nyx**  
   https://github.com/CTCaer/hekate/releases/latest

3. **TegraRcmGUI** — payload injection + SD USB mass storage tools  
   https://github.com/eliboa/TegraRcmGUI/releases/latest

4. **Fedora 42 Switchroot image**  
   Download `switch-fedora-kde-42-2026-05-26.7z` from:  
   https://download.switchroot.org/fedora-42/

> We also used OmniRCM during the original setup. TegraRcmGUI can handle the same core Windows tasks, so this guide uses it where possible to keep the tool list shorter.

---

## 2. Confirm the Switch is unpatched

Use the serial tables in the current NH Switch Guide:

https://switch.hacks.guide/user_guide/getting_started

An unpatched V1 can use the Tegra X1 bootROM Fusée Gelée exploit regardless of modern Switch firmware version.

---

## 3. Enter RCM

### Option A — normal/safest method

Use the current NH RCM guide:

https://switch.hacks.guide/user_guide/rcm/entering_rcm

The normal method grounds pin 10 on the right Joy-Con rail while booting with Volume Up.

### Option B — no-jig method we used: remove the eMMC

The original Fusée Gelée disclosure documents **unseating the socketed eMMC board** as a way to stop the Switch from finding boot media and force it into RCM.

#### Safety rules

- Fully power off the Switch.
- Unplug USB-C.
- Disconnect the battery before unplugging/reconnecting internal boards.
- Never hot-plug the eMMC or microSD reader.
- Keep metal tools away from the powered motherboard.
- Do not touch the eMMC gold contacts.

#### Basic sequence

1. Power the Switch fully off.
2. Remove both Joy-Cons, game card and microSD.
3. Open the rear shell using the correct tri-point/JIS drivers.
4. Remove the microSD reader carefully.
5. Remove the metal shield.
6. Disconnect the Switch battery.
7. Locate the small removable **eMMC daughterboard** near the fan/heatpipe.
8. Lift the eMMC board **straight up**, keeping it level.
9. Reconnect the battery.
10. Connect USB-C to the PC.
11. Press Power once.

The Switch screen should stay **black**. A black screen alone is not proof; the PC should detect the Switch as an **APX/RCM** device.

> [!IMPORTANT]
> If you intend to keep using this no-jig setup, leaving the eMMC out makes the Switch fall into RCM on cold boot, but Nintendo's normal OS is unavailable until the eMMC is safely reinstalled. You will still need to inject a payload after a complete shutdown.

---

## 4. Inject Hekate

1. Install/open TegraRcmGUI.
2. Install the APX driver if needed.
3. Confirm the Switch shows **RCM OK**.
4. Select the current `hekate_ctcaer_*.bin` payload from the Hekate release.
5. Inject the payload.

If the microSD does not contain Hekate's full files yet, Hekate may show errors about missing LP0/Minerva/SD files. That is fixable in the next step.

---

## 5. Put Hekate files on the microSD

From the latest Hekate release ZIP, copy the entire:

```text
bootloader
```

folder to the **root of the microSD**.

The root should look roughly like:

```text
SD CARD
├── bootloader
└── Nintendo        (if the card was previously used by HOS)
```

### No microSD slot on the PC?

TegraRcmGUI can expose the Switch microSD as USB mass storage using its **Memloader / SD Card** tool.

Once Hekate/Nyx is fully working, you can also use:

```text
Hekate → Tools → USB Tools → SD Card
```

---

## 6. Back up the microSD

Before partitioning:

1. Mount the SD over Hekate USB Mass Storage.
2. Copy **everything** from the microSD to a backup folder on the PC.
3. Safely eject the drive.

Hekate's partitioning step will erase/rebuild the SD layout.

---

## 7. Create the Linux partition

In Hekate:

```text
Tools → Partition SD Card
```

Example layout used on a ~256 GB card:

```text
Linux (EXT4): 64 GiB
Android:       0 GiB
emuMMC:        0 GiB
HOS/FAT32:     remaining space (~168 GiB)
```

64 GiB is a comfortable Fedora partition. Adjust it to your card size and needs.

Confirm the partition operation and let Hekate finish completely.

---

## 8. Copy Fedora 42 files

1. In Hekate Partition Manager, use **SD UMS** or mount the SD with USB Tools.
2. On Windows, open the Fedora archive with **7-Zip File Manager**.
3. Do **not** use Windows' built-in 7z extraction.
4. Extract everything from:

```text
switch-fedora-kde-42-2026-05-26.7z
```

straight to the FAT32 root of the Switch SD.

You should see folders like:

```text
SD CARD
├── bootloader
└── switchroot
```

Do not put them inside an extra `switch-fedora-...` folder.

Safely eject the SD when extraction finishes.

---

## 9. Flash Fedora

In Hekate:

```text
Tools → Partition SD Card → Flash Linux
```

Confirm the Fedora installer files and let the flash reach **100%**.

Do not power off, remove the SD or disconnect power while it is flashing.

---

## 10. First Fedora boot

Go back to Hekate Home:

```text
More Configs → L4T Fedora
```

The first boot will show the Switchroot boot screen and then Fedora's initial setup.

Create a **normal user account with admin privileges**. Do not create only a root account.

Switchroot documents the `+` button on a connected Joy-Con as a shortcut for the on-screen keyboard during initial setup.

After setup, run all normal Fedora 42 updates through **Discover** or `dnf`, but do **not** perform a major upgrade to Fedora 43.

---

## 11. Auto-boot Fedora after Hekate injection

In Hekate/Nyx:

```text
Options → Auto Boot
```

Select the **L4T Fedora** entry from the INI/More Configs list.

Set:

```text
Boot Time Delay: 0 seconds
```

Save Options.

After that, injecting Hekate will immediately launch Fedora instead of stopping at the Hekate menu.

To interrupt auto-boot and return to Hekate, hold the appropriate volume key while Hekate starts.

### Important limitation

This does **not** remove the need to inject Hekate after a full shutdown on an unpatched V1 without a modchip. Hekate is part of the L4T Linux boot chain.

Using **sleep** instead of full shutdown makes the Switch feel much more appliance-like.

---

## 12. Reassembly / eMMC note

If you used the eMMC-removal RCM method:

- Always power completely off, unplug USB and disconnect the battery before touching the eMMC.
- Reinstalling the eMMC restores the ability to boot Nintendo's normal OS, but you still need another way to enter RCM later unless you have configured an appropriate boot method.
- Leaving the eMMC out is an advanced Linux-focused setup and means stock HOS cannot boot.

Before final reassembly, make sure the thermal interface/compound disturbed by removing the shield is restored correctly.

---

# Optional post-install software

## Steam on ARM64 Switch — Switchdeck

The Switch is ARM64/aarch64, so normal x86_64 Steam does not run natively. Switchdeck provides a Switch-specific Steam/Box64 setup.

Project:

https://github.com/SildurFX/Switchdeck

Basic install:

1. Download `install-steam.sh`.
2. Open a terminal in the download folder.
3. Run the script.
4. Launch Steam using the Switchdeck shortcut/script.
5. In Steam Compatibility, use **Proton Experimental** or the current Switchdeck-recommended ARM/FEX Proton option.
6. Restart Steam after compatibility changes so Switchdeck can apply its patches.

Remember: the Switch only has **4 GB of shared RAM**, so many modern PC games will not run well or at all.

## Lua development tools

Fedora 42:

```bash
sudo dnf5 install lua luarocks
```

Check versions:

```bash
lua -v
luarocks --version
```

These are normal Lua development tools. They are **not** the Windows-only LuaTools Steam plugin.

---

# Optional customization

## Custom Hekate/Fedora boot logo

On our Fedora install, the L4T Fedora boot entry used:

```text
switchroot/fedora/logo_fedora.bmp
```

Back up the original file first. Hekate boot logos use a specific BMP format, so follow Hekate's current bootlogo documentation before replacing it:

https://github.com/CTCaer/hekate/blob/master/README_BOOTLOGO.md

The later pink **Switchroot "Booting..."** screen is a different stage of the L4T boot stack and is not just this BMP.

## Startup sound in KDE

A simple approach:

1. Put your legally-owned/custom `.wav` file somewhere in your home folder, for example:

```text
~/.local/share/sounds/startup.wav
```

2. Create a script such as:

```bash
#!/bin/bash
sleep 3
pw-play /home/YOUR_USERNAME/.local/share/sounds/startup.wav
```

3. Make it executable:

```bash
chmod +x ~/Desktop/startup.sh
```

4. Add it in:

```text
System Settings → Autostart → Add New → Login Script
```

---

# Troubleshooting quick notes

### Black screen after pressing Power

That can be correct for RCM. Confirm the PC sees **APX/RCM** before assuming it worked.

### Hekate says `Failed to init or mount SD!`

Check that:

- the microSD is actually inserted,
- the microSD reader is seated correctly,
- the card is readable/FAT32 on its HOS partition.

### Hekate reports missing LP0 / Minerva libraries

Copy the **entire latest Hekate `bootloader` folder** to the root of the microSD.

### Windows says a Fedora filename is invalid during extraction

Use **7-Zip File Manager**, not Windows' built-in archive extractor.

### Fedora cursor moves by itself

We saw this when Fedora detected a Joy-Con with stick drift and mapped it as mouse input. Boot with the Joy-Cons detached to confirm, then test controllers individually.

### Steam game is missing

Switchdeck/Proton cannot create a Steam license. Games must be owned/shared normally by Steam or added as legitimate non-Steam software. Compatibility also varies heavily on ARM64.

---

# Main project links

- NH Switch Guide — https://switch.hacks.guide/
- Hekate — https://github.com/CTCaer/hekate
- Switchroot Wiki — https://wiki.switchroot.org/wiki/linux
- Fedora 42 guide — https://wiki.switchroot.org/wiki/linux/l4t-fedora-installation-guide-2
- Fedora 42 download — https://download.switchroot.org/fedora-42/
- TegraRcmGUI — https://github.com/eliboa/TegraRcmGUI
- 7-Zip — https://www.7-zip.org/
- Switchdeck — https://github.com/SildurFX/Switchdeck

---

## Disclaimer

This is an unofficial community guide based on one successful Switch V1 Fedora setup. It is not affiliated with Nintendo, Switchroot, Hekate, Fedora, Valve or the other linked projects. Follow the official project documentation when it differs from this guide.