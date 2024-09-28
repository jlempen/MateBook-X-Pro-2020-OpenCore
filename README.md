![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# MateBook-X-Pro-2020-OpenCore
macOS on the Huawei MateBook X Pro 2020 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).

The MateBook X Pro 2020 is a nearly perfect Hackintosh laptop. It looks almost like the real thing once you remove all the ugly Intel, Huawei and NVIDIA stickers from the palmrest. It sounds great with its 4 speakers, the huge trackpad supports all the native gestures and feels like a Mac trackpad, the keyboard is almost as good as the real thing, the 13.9 inch display looks amazing, the MateBook sleeps and wakes quickly and the battery life is on par with an Intel MacBook Pro/Air. It feels like a MacBook Pro/Air. I switch daily between a 2020 M1 MacBook Pro and the MateBook X Pro 2020 and very often wouldn't know which device I'm currently working with.

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources.

This EFI folder is based on the now defunct [repository from GitHub user RepoWeaver](https://github.com/RepoWeaver/MateBook-X-Pro-2020-OpenCore). Fortunately for us, [GitHub user Thyodas](https://github.com/Thyodas) uploaded his local backup copy of RepoWeaver's EFI folder to his [GitHub repository](https://github.com/Thyodas/MateBook-X-Pro-2020-OpenCore). 
So I downloaded his EFI folder, updated OpenCore and all the relevant SSDTs and kexts to their latest versions as of October 2023 and changed a few things, such as replacing the ALC256 layout with `layout-id 76`, which enables all four speakers without the need to create an aggregate audio device, moving a few iGPU-related WhateverGreen quirks from the boot-args and adding a few more WhateverGreen quirks to `DeviceProperties/Add/PciRoot(0x0)/Pci(0x2,0x0)`. I also rebuilt the `USBMap.kext` in Windows with the excellent [USBToolbox tool](https://github.com/USBToolBox/tool).

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

For macOS to be able to boot on the MateBook X Pro 2020, the `Secure Boot` option  _**must be disabled**_ in the UEFI Settings.

To boot from the USB stick containing the macOS installer, power on your MateBook X Pro 2020 and press and hold the `F12 Key` as soon as the Huawei Logo is displayed, then choose the USB stick in the list.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a MateBook X Pro 2020 with this repository's EFI folder.

`AirportItlwm_Ventura.kext`, `AirportItlwm_Sonoma14.0.kext` and `AirportItlwm_Sonoma14.4.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura or Sonoma depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. 

As the Intel Wifi chip does not yet work with `AirportItlwm.kext` in macOS Sequoia, you'll need to use `Itlwm.kext` and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) to connect to a Wifi network. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/MateBook-X-Pro-2020-OpenCore/blob/main/Tools/HeliPort_v1.5.dmg) of this repo. This EFI will dynamically load `Itlwm.kext` instead of `AirportItlwm.kext` when you boot into macOS Sequoia.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

This repository uses the unofficial OpenCore_NO_ACPI_Build fork of OpenCore by [btwise](https://gitee.com/btwise/OpenCore_NO_ACPI), wich is not endorsed by Acidanthera (the dev team behind OpenCore). The main (and only) difference between this fork and the official OpenCore version is that it allows to prevent ACPI injection (e.g. patches, tables, boot parameters) into other OSes besides macOS.

<details>
  <summary>Software Specifications</summary>
  
## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 15 Sequoia, 14 Sonoma and 13 Ventura |
| OpenCore         | [MOD-OC v1.0.2](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.2_e9b40ba/OpenCore-Mod-1.0.2-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| UEFI Firmware    | v1.26 |
| SSD format       | APFS file system, GPT partition table |
</details>

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
- [x] CPU power management (`CPUFriend.kext` with `CPUFriendDataProvider.kext`)
- [x] CPU SpeedStep (`CPUFriend.kext` with `CPUFriendDataProvider.kext`)
- [x] iGPU with full acceleration (`WhateverGreen.kext`, `AAPL,ig-platform-id 0400A53E`, `device-id A53E0000`)
- [x] SSD drive (`NVMeFix.kext`)
- [x] Sleep/hibernate and wake
- [x] Rear left USB-C port with hotplug (`USBMap.kext`)
- [x] Front left USB-C port with hotplug for USB 1.1 and USB 2.0 devices only (`USBMap.kext`)
- [x] Right USB 3.0 port with hotplug (`USBMap.kext`)
- [x] WLAN (`AirportItlwm.kext`)
- [x] Bluetooth (`IntelBluetoothFirmware.kext` with `IntelBTPatcher.kext` and `BlueToolFixup.kext`)
- [x] Nose-Camera VID 0x05c8 PID 0x03c0 (don't forget to pop it up :-)
- [x] Internal quad speakers, microphone and Combojack (`AppleALC.kext`, `alcid=76`)
- [x] Keyboard with working brightness, volume and mute keys (`VoodooPS2.kext`)
- [x] Trackpad with native multi-touch gestures (`VoodooI2C.kext` with `VoodooI2CHID.kext`)
- [x] Touchscreen (`VoodooI2C.kext` with `VoodooI2CHID.kext`) (disabled for now)
- [x] Battery percentage and cycle count (`VirtualSMC.kext` with `SMCBatteryManager.kext`)
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
  <summary>Undervolting to reduce heat and improve performance</summary>
  
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
The latest version 2.3.0 of itlwm.kext is already included in the Kext folder and `config.plist` file.
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
