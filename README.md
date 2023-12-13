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

For macOS to be able to boot on the MateBook X Pro 2020, the `Secure Boot` option needs to be _**disabled**_ in the BIOS.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` files. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a MateBook X Pro 2020 with this repository's EFI folder.

This repository features an EFI folder with two distinct `config.plist` files. One is meant to be used without the UEFI BIOS hacks below (`config_easy.plist`), the other one (`config_hard.plist`) will only boot after the UEFI hacks have been applied. Simply rename the config file you plan to use to `config.plist` and delete the other config file.

## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 14.2 Sonoma |
| OpenCore         | [OC v0.9.5](https://github.com/acidanthera/OpenCorePkg/releases/download/0.9.5/OpenCore-0.9.5-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| UEFI Firmware    | v1.26 |
| SSD format       | APFS file system, GPT partition table |

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
| Camera | 1 MPix recessed camera |
| Keyboard / Trackpad | |
| Display | 13.90 inch 3:2, 3000 x 2000 LTPS 260 PPI, 10-Point Capacitive |
| Battery | 56 Wh |
| Accelerometers, gyroscopes, ambient light sensors | |

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
- [x] Nose-Camera (don't forget to pop it up :-)
- [x] Internal quad speakers, microphone and Combojack (`AppleALC.kext`, `alcid=76`)
- [x] Keyboard with working brightness, volume and mute keys (`VoodooPS2.kext`)
- [x] Trackpad with native multi-touch gestures (`VoodooI2C.kext` with `VoodooI2CHID.kext`)
- [x] Touchscreen (`VoodooI2C.kext` with `VoodooI2CHID.kext`) (disabled for now)
- [x] Battery percentage and cycle count (`VirtualSMC.kext` with `SMCBatteryManager.kext`)
- [x] USB Type-C Power Delivery

## What needs some more work
- [ ] Thunderbolt
- [ ] Front left USB-C port hotplug with USB 3.x devices (depends on Thunderbolt)
- [ ] Accelerometers, gyroscope
- [ ] Ambient light sensor

## What will probably never work
- [ ] NVIDIA GeForce MX250 dGPU (disabled with an SSDT)
- [ ] Fingerprint sensor (disabled in the UEFI BIOS)

## UEFI Firmware hacks
The UEFI Firmware UI of the MateBook X Pro 2020 shows only a few trivial settings. In order to take advantage of better CPU power management and graphics acceleration, there are a few other settings that need to be changed in the UEFI BIOS. The best way to achieve this is to use the [RU.efi tool](http://ruexe.blogspot.com) or the new [setup_var.efi tool](https://github.com/datasone/setup_var.efi). The [Dortania OpenCore Post-Install guide](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#turning-off-cfg-lock-manually) has detailed instructions on how to use the RU.efi tool.

Hint: the RU.efi tool is included in this EFI folder and may be launched from the OpenCore picker in the Auxiliary tools section.

To access the top bar menu in RU.efi, use the `ALT` key together with the red letter corresponding to the desired menu option. To change the page in `CpuSetup` and `SaSetup`, use the `CTRL+Fn+Up` and `CTRL+Fn+Down` key combinations. To write the values, use the `CTRL+W` and to quit RU.efi use the `ALT+Q` key combinations.

_**Please be aware that applying these hacks incorrectly may brick your computer! Proceed carefully and only if you know what you are doing!**_

### UEFI Firmware variables which need to be modified
| VarName | VarOffset | VarStore | From | To |
| ---------------- | -- | -- | --------- | --------- |
| CFG Lock | 0x3E | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| Overclocking Lock | 0xDA | 0x3 (CpuSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| VT-d | 0x10B | 0x2 (SaSetup) | 0x1 (Enabled) | 0x0 (Disabled) |
| DVMT Pre-Allocated | 0x107 | 0x2 (SaSetup) | 0x1 (32M) | 0x2 (64M) |
| DVMT Total Gfx Mem | 0x108 | 0x2 (SaSetup) | 0x2 (256M) | 0x3 (MAX) |

## Enabling native HiDPI display settings in macOS
On the installed macOS system, the default display resolution is already "Retina", as the native 3000x2000 resolution is scaled down to 1500x1000. To enable a few more native HiDPI settings in the Display Preferences of macOS, download and run the [one-key-hidpi](https://github.com/jlempen/one-key-hidpi) script and select the option `(6) 3000x2000 Display`.

## Related repositories
* https://github.com/RepoWeaver/MateBook-X-Pro-2020-OpenCore
* https://github.com/Thyodas/MateBook-X-Pro-2020-OpenCore
* https://github.com/Chilluminati91/Huawei-Matebook-X-Pro-2020-Hackintosh
* https://github.com/mrjohndoe01/Huawei-Matebook-X-Pro-Hackintosh
* https://github.com/ske1996/matebook-x-2020-Hackintosh-OpenCore
* https://github.com/tmbt78/Huawei-Matebook-X-2020-Hackintosh
