![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# MateBook-X-Pro-2020-OpenCore
macOS on the Huawei MateBook X Pro 2020 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).

> [!WARNING]
> ### Installing or upgrading to macOS Tahoe
> 
> When  <ins>**upgrading**</ins> your existing installation to macOS Tahoe, you <ins>**MUST**</ins> log out of your `Apple Account` (iCloud) before proceeding with the upgrade. Furthermore make sure you <ins>**DESELECT**</ins> the option to enable `FileVault` disk encryption in the installer and do not sign into your `Apple Account` (iCloud) until the installer is done and you reach the desktop. Failing to do so will eventually encrypt your disk and prevent you from unlocking your disk with your password on restart.
>
> If `FileVault` disk encryption is enabled in your existing installation of macOS and you wish to keep it enabled in macOS Tahoe, then [carefully read the section below](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main#fixing-filevault-when-upgrading-to-macos-tahoe) before proceeding with the upgrade.
>
> When doing a <ins>**clean install**</ins> of macOS Tahoe, make sure you <ins>**DESELECT**</ins> the option to enable `FileVault` disk encryption in the installer and do not sign into your `Apple Account` (iCloud) until the installer is done and you reach the desktop. Failing to do so will eventually encrypt your disk and prevent you from unlocking your disk with your password on restart.
>
> If you wish to enable `FileVault` disk encryption in macOS Tahoe, [carefully read the section below](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main#fixing-filevault-when-upgrading-to-macos-tahoe).

## Latest News
* (20260112) Added the `apfs_aligned.efi` driver to fix `FileVault` when upgrading to macOS Tahoe ([see section below](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main#fixing-filevault-when-upgrading-to-macos-tahoe)).
* (20260112) Added resources and instructions to enable `AirportItlwm.kext` on macOS Sequoia and Tahoe ([see section below](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe)).
* (20260112) Fixing audio in macOS Tahoe ([see section below](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main#fixing-audio-on-macos-tahoe)).
* (20260112) With the stuff merged today, macOS Tahoe 26.2 now installs and runs quite nicely.

## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 26 Tahoe, 15 Sequoia, 14 Sonoma and 13 Ventura |
| OpenCore         | [MOD-OC v1.0.7](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.7_ffbd7f5/OpenCore-Mod-1.0.7-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| UEFI Firmware    | v1.26 |
| SSD format       | APFS file system, GPT partition table |

## Abstract
The MateBook X Pro 2020 is a nearly perfect Hackintosh laptop. It looks almost like the real thing once you remove all the ugly Intel, Huawei and NVIDIA stickers from the palmrest. It sounds great with its 4 speakers, the huge trackpad supports all the native gestures and feels like a Mac trackpad, the keyboard is almost as good as the real thing, the 13.9 inch display looks amazing, the MateBook sleeps and wakes quickly and the battery life is on par with an Intel MacBook Pro/Air. It feels like a MacBook Pro/Air. I switch daily between a 2020 M1 MacBook Pro and the MateBook X Pro 2020 and very often wouldn't know which device I'm currently working with.

> [!TIP]
> To boot from the USB stick containing the macOS installer, power on your MateBook X Pro 2020 and press and hold the `F12 Key` as soon as the Huawei Logo is displayed, then choose the USB stick in the list.

> [!IMPORTANT]
> For macOS to be able to boot on the MateBook X Pro 2020, the `Secure Boot` option  _**must be disabled**_ in the UEFI Settings.

> [!IMPORTANT]
> Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a MateBook X Pro 2020 with this repository's EFI folder.

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources.

This EFI folder is based on the now defunct [repository from GitHub user RepoWeaver](https://github.com/RepoWeaver/MateBook-X-Pro-2020-OpenCore). Fortunately for us, [GitHub user Thyodas](https://github.com/Thyodas) uploaded his local backup copy of RepoWeaver's EFI folder to his [GitHub repository](https://github.com/Thyodas/MateBook-X-Pro-2020-OpenCore). 
So I downloaded his EFI folder, updated OpenCore and all the relevant SSDTs and kexts to their latest versions as of October 2023 and changed a few things, such as replacing the ALC256 layout with `layout-id 76`, which enables all four speakers without the need to create an aggregate audio device, moving a few iGPU-related WhateverGreen quirks from the boot-args and adding a few more WhateverGreen quirks to `DeviceProperties/Add/PciRoot(0x0)/Pci(0x2,0x0)`. I also rebuilt the `USBMap.kext` in Windows with the excellent [USBToolbox tool](https://github.com/USBToolBox/tool).

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

`AirportItlwm-Ventura.kext`, `AirportItlwm-Sonoma140.kext`, `AirportItlwm-Sonoma144.kext` and `AirportItlwm-Sequoia-Tahoe.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are drivers required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura, Sonoma, Sequoia or Tahoe depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. 

In macOS Sequoia and Tahoe, you'll need to apply root patches with [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases) once the OS is up and running in order to enable the Intel Wifi chip. [Head over to the instructions.](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe).

the `Itlwm.kext` driver and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) are included but disabled in this EFI for those who prefer to connect to their Wifi network this way. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/blob/main/Tools/HeliPort.dmg) of this repo.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

This repository uses the unofficial OpenCore_NO_ACPI_Build fork of OpenCore by [btwise](https://gitee.com/btwise/OpenCore_NO_ACPI), wich is not endorsed by Acidanthera (the dev team behind OpenCore). The main (and only) difference between this fork and the official OpenCore version is that it allows to prevent ACPI injection (e.g. patches, tables, boot parameters) into other OSes besides macOS.

<details>
  <summary>Computer Specifications</summary>
  
## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-10510U (1.8 - 4.9 GHz, Comet Lake) |
| iGPU             | Intel UHD Graphics 620 |
| dGPU             | NVIDIA GeForce MX250 |
| Audio            | Realtek ALC 256 |
| RAM              | 2x8 GB LPDDR3 2133 MHz |
| Wifi + Bluetooth | Wifi 5 AC9560, Bluetooth 5.0 |
| Storage          | Samsung PM981 NVMe PCIe 1 TB SSD (unsupported), replaced with a Western Digital SN850 NVMe PCIe 1 TB SSD |
| USB Type-C | Supports Power Delivery and DisplayPort |
| USB-A 3.0| |
| Camera | 1 MPix recessed camera VID 0x05c8 PID 0x03c0 |
| Keyboard / Trackpad | |
| Display | 13.90 inch 3:2, 3000 x 2000 LTPS 260 PPI, 10-Point Capacitive |
| Battery | 56 Wh |
| Accelerometers, gyroscopes, ambient light sensors | |
</details>

<details>
  <summary>What works</summary>
  
## What works
- [x] CPU power management
- [x] CPU SpeedStep
- [x] iGPU with full acceleration (`AAPL,ig-platform-id 0400A53E`, `device-id A53E0000`)
- [x] SSD drive
- [x] Sleep/hibernate and wake
- [x] Rear left USB-C port with hotplug
- [x] Front left USB-C port with hotplug for USB 1.1 and USB 2.0 devices only
- [x] Right USB 3.0 port with hotplug
- [x] WLAN
- [x] Bluetooth
- [x] Nose-Camera VID 0x05c8 PID 0x03c0 (don't forget to pop it up :-)
- [x] Internal quad speakers, microphone and Combojack (`alcid=76`)
- [x] Keyboard with working brightness, volume and mute keys
- [x] Trackpad with native multi-touch gestures
- [x] Touchscreen (disabled for now)
- [x] Battery percentage and cycle count
- [x] USB Type-C Power Delivery
</details>

<details>
  <summary>What needs some more work</summary>
  
## What needs some more work
- [ ] Thunderbolt
- [ ] Front left USB-C port hotplug with USB 3.x devices (depends on Thunderbolt). Using a USB-C hub on this port causes a kernel panic and restart after unplugging the hub. Using the port for power delivery seems to work fine, though.
- [ ] Accelerometers, gyroscope
- [ ] Ambient light sensor
</details>

<details>
  <summary>What will probably never work</summary>
  
## What will probably never work
- [ ] NVIDIA GeForce MX250 dGPU (disabled with an SSDT)
- [ ] Fingerprint sensor (disabled in the UEFI BIOS)
</details>

<details>
  <summary>UEFI Settings</summary>
  
## UEFI Settings
To enter the UEFI Settings, power on your MateBook X Pro 2020 and press and hold the `F2 Key` as soon as the Huawei Logo is displayed on the screen.

The `Secure Boot` setting ***must be disabled to boot macOS***.

All other settings may remain on their default values and won't prevent macOS from booting, but keep in mind that every disabled device saves power and increases the battery runtime. For example, as the fingerprint reader won't work in macOS, disabling the device in the UEFI Settings is recommended unless you plan on using another operating system on the device as well. 
</details>

<details>
  <summary>Enabling the Intel Wireless Card in macOS Sequoia and Tahoe</summary>
  
## Enabling the Intel Wireless Card in macOS Sequoia and Tahoe

### Using the AirportItlwm.kext driver and root patching
It is now possible to have Intel WiFi with native Airport features on macOS Sequoia and Tahoe by enabling the `AirportItlwm.kext` driver for macOS Ventura provided by [the OpenIntelWireless project](https://openintelwireless.github.io/). 

`AirportItlwm.kext` uses Apple's IO80211Family. It provides certain Airport features but lacks stability compared with `itlwm.kext` due to the ambiguity of reverse engineering.

To enable the `AirportItlwm.kext` driver, download and install the latest release of [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases).

Launch the OCLP-Mod Patcher, this may take a few seconds. As of January 2026, the tool is only available in Chinese.

Now click on the upper right button to select the Root Patching option:
<img width="712" height="443" alt="Screenshot 2026-01-10 at 00 43 16" src="https://github.com/user-attachments/assets/3af5464a-836b-4f71-a0fc-8ac8bd4af304" />

Then click on the highlighted button or press Enter to start the patching process:
<img width="712" height="443" alt="Screenshot 2026-01-10 at 00 46 51" src="https://github.com/user-attachments/assets/843687df-655f-47ea-bfc8-5b3f06681756" />

Once the patching is done, click on the highlighted button or press Enter to close the tool and restart your computer. Your Intel wireless card should be working now.

> [!IMPORTANT]
> You'll need to repeat the above steps after every macOS update!

### Using the Itlwm.kext driver
`itlwm.kext` uses Apple's IOEthernet rather than IO80211. It is purely based on open-source resources, provides a stabler and faster performance, and the ability to unload on Kernels that use prelined kernel.

To enable Intel WiFi with the `itlwm.kext` driver, open the `Kernel -> Add` tab in your `config.plist` file.

Enable the following kext:
```
itlwm.kext
```

Then disable the following kexts:

```
IOSkywalkFamily.kext
IO80211FamilyLegacy.kext
IO80211FamilyLegacy.kext/Contents/PlugIns/AirPortBrcmNIC.kext
AMFIPass.kext
AirportItlwm-Sequoia-Tahoe.kext
```

Now head over to the `Kernel -> Block` tab and disable the `com.apple.iokit.IOSkywalkFamily` item.

Then head over to the `NVRAM` tab, select the `7C436110-AB2A-4BBB-A880-FE41995C9F82` UUID and remove the `-amfipassbeta` argument from the `boot-args` key.

Save and close the `config.plist` file and reboot your computer.

Download and install the latest `HeliPort` Intel WiFi client for `itlwm` from [the OpenIntelWireless project](https://openintelwireless.github.io/HeliPort/#download).

Add the `HeliPort` client to your login items and hide the macOS WiFi icon from the Menu Bar.
</details>

<details>
  <summary>Fixing audio on macOS Tahoe</summary>
  
## Fixing audio on macOS Tahoe

### By root patching
As Apple removed the `AppleHDA.kext` from macOS Tahoe, [Acidanthera's AppleALC.kext](https://github.com/acidanthera/AppleALC) won't work on macOS Tahoe out of the box and audio is broken. To fix this, simply run [Laobamac's OCLP-Mod Patcher](https://github.com/laobamac/OCLP-Mod/releases) a second time once you have [fixed the Intel wireless card](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/tree/main?tab=readme-ov-file#enabling-the-intel-wireless-card-in-macos-sequoia-and-tahoe) and Internet is working again. The reason for this is that the OCLP-Mod Patcher needs to download the Kernel Development Kit from Apple's servers in order to reinstall the `AppleHDA.kext` on your macOS Tahoe partition and to do so, it needs a working Internet connection. 

### By installing the VoodooHDA audio driver
Another way to get back the internal speakers and microphone on macOS Tahoe is to install [SergeySlice's VoodooHDA audio driver](https://github.com/CloverHackyColor/VoodooHDA) with [chris1111's convenient VoodooHDA-Tahoe installer](https://github.com/chris1111/VoodooHDA-Tahoe). This has the added benefit of fixing the loud click you hear from your speakers when playing sound for the first time after waking your SL3 from hibernation.

Before installing the VoodooHDA driver, you need to disable the `AppleALC.kext` driver in your `config.plist` file under `Kernel -> Add` and reboot your computer.

Then [grab the latest installer](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/blob/main/Tools/VoodooHDA-Tahoe.pkg) from the Tools folder in my repository, launch the installer and follow the instructions.

Once you're back in macOS Tahoe after a reboot, head over to `System Settings -> Sound -> Output & Input` and select the `Output` tab, then select `Speaker (Analog)` as your sound output device.
</details>

<details>
  <summary>Undervolting to reduce heat and improve performance</summary>

<details>
  <summary>Fixing FileVault when upgrading to macOS Tahoe</summary>
  
## Fixing FileVault when upgrading to macOS Tahoe
If `FileVault` disk encryption is enabled during the upgrade or install of macOS Tahoe, the `FileVault` login window will reject your password and the encrypted disk will remain locked after the first reboot. This is due to Tahoe's APFS filesystem driver not supporting the software `FileVault` disk encryption created with previous versions of macOS.

There are two solutions to this rather annoying issue:

### Enabling the older APFS driver from macOS Sequoia
Open the `UEFI -> Drivers` tab in your `config.plist` file and enable the `apfs_aligned.efi` driver.

Then head over to the `UEFI -> APFS` tab and disable the `EnableJumpstart` option.

Save and close the `config.plist` file and restart your computer. The `FileVault` login window should now accept your password and unlock the disk.

### Turning off FileVault in the macOS Recovery Console
Follow the nice instructions [on Jac Timms' website](https://www.ichi.co.uk/blog/removing-file-vault-from-internet-recovery-console).
</details>

## Undervolting to reduce heat and improve performance
The `VoltageShift.kext` undervolting tool is already included and enabled in this repository's `Kexts` folder. To be able to launch the `voltageshift` command line tool from anywhere, copy [VoltageShift from the Tools folder](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/blob/main/Tools/VoltageShift-EFI.zip) to your `/usr/local/bin` folder by entering `sudo cp voltageshift /usr/local/bin/` in the macOS terminal after navigating to the folder where you downloaded and unzipped the `voltageshift` executable file.

Please refer to the instructions found in the [VoltageShift repository](https://github.com/sicreative/VoltageShift), as well as to the excellent howto found in [zearp's repository](https://github.com/zearp/Nucintosh#undervolting).

Once you have found an undervolting configuration that works well on your device, make it permanent by entering the following command in the terminal after navigating to the folder where you downloaded and unzipped the `VoltageShift-EFI.zip` file:
> sudo ./voltageshift buildlaunchd -120 -50 -80 0 0 0 1 28 18 0.002 60

The above undervolting values are only an example and shouldn't be used on your system.
</details>

<details>
  <summary>Disabling CFG Lock to improve Power Management</summary>
  
## Disabling CFG Lock to improve Power Management
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Disable CFG Lock`
4. Press enter
5. Restart

To verify this worked, press Space and select the `Check CFG Lock State` tool -- if it was successful, you'll see:

> This firmware has UNLOCKED MSR 0xE2 register!

Finally, edit `config.plist` and set `Kernel -> Quirks -> AppleCpuPmCfgLock` and `Kernel -> Quirks -> AppleXcpmCfgLock` to `false` and reboot.
</details>

<details>
  <summary>Disabling Overclocking Lock to enable Undervolting</summary>
  
## Disabling Overclocking Lock to enable Undervolting
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Disable Overclocking Lock`
4. Press enter
5. Restart
</details>

<details>
  <summary>Increasing DVMT Gfx Memory to improve iGPU performance</summary>
  
## Increasing DVMT Gfx Memory to improve iGPU performance
1. Boot into OpenCore
2. Press Space to see the list of tools
3. Select `Set DVMT to 64M`
4. Press enter
5. Then select `Set Total GFX Mem to MAX`
6. Press enter
7. Restart

Finally, edit `config.plist` and delete or comment out `framebuffer-fbmem` and `framebuffer-stolenmem` in `Device Properties -> Add -> PciRoot(0x0)/Pci(0x2,0x0)` and restart.
</details>

<details>
  <summary>More information on the modified UEFI Firmware variables</summary>
  
## More information on the modified UEFI Firmware variables 
| VarName | VarOffset | VarStore | From | To |
| ---------------- | -- | -- | --------- | --------- |
| CFG Lock | 0x3E | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| Overclocking Lock | 0xDA | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| VT-d | 0x10B | 0x2 (SaSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| DVMT Pre-Allocated | 0x107 | 0x2 (SaSetup) | 0x1 (32M) | 0x2 (64M) |
| DVMT Total Gfx Mem | 0x108 | 0x2 (SaSetup) | 0x2 (256M) | 0x3 (MAX) |

To revert all changes made to the UEFI Firmware variables to their default values, enable the corresponding entries in the `config.plist` file under `Misc` -> `Tools`, restart to the OpenCore menu, press space to see the list of tools and revert the changes by launching the option you wish to revert to its default value:
- `Enable CFG Lock`
- `Enable Overclocking Lock`
- `Set DVMT to default (32M)`
- `Set Total GFX Mem to default (256M)`

Repeat for every UEFI variable you wish to revert to its default value.

***Please be aware that you need to revert any changes made to your `config.plist` file before reverting the UEFI variables to their default values, or macOS won't boot anymore!***
</details>

<details>
  <summary>Enabling native HiDPI display settings in macOS</summary>
  
## Enabling native HiDPI display settings in macOS
On the installed macOS system, the default display resolution is already "Retina", as the native 3000x2000 resolution is scaled down to 1500x1000. To enable a few more native HiDPI settings in the Display Preferences of macOS, download and run the [one-key-hidpi](https://github.com/jlempen/one-key-hidpi) script and select the option `(6) 3000x2000 Display`.
I also recommend downloading and installing [BetterDisplay](https://github.com/waydabber/BetterDisplay) to change and manage the display resolutions on the MateBook X Pro 2020.
</details>

<details>
  <summary>Fixing broken Apple Messages and FaceTime</summary>
  
## Fixing broken Apple Messages and FaceTime
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma and Sequoia, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg) instead.
The latest version 2.3.0 of `itlwm.kext` is already included in the Kext folder and `config.plist` file.

In addition to the above, to enable `itlwm.kext` under macOS Ventura and macOS Sonoma, you need to delete any text (i.e. `24.0.0` and `24.99.99` respectively) in the `MinKernel` and `MaxKernel` fields under `Kernel -> Add -> itlwm.kext` in your `config.plist` file.
</details>

<details>
  <summary>Related repositories</summary>
  
## Related repositories
* https://github.com/RepoWeaver/MateBook-X-Pro-2020-OpenCore
* https://github.com/Thyodas/MateBook-X-Pro-2020-OpenCore
* https://github.com/Chilluminati91/Huawei-Matebook-X-Pro-2020-Hackintosh
* https://github.com/mrjohndoe01/Huawei-Matebook-X-Pro-Hackintosh
* https://github.com/ske1996/matebook-x-2020-Hackintosh-OpenCore
* https://github.com/tmbt78/Huawei-Matebook-X-2020-Hackintosh
</details>
