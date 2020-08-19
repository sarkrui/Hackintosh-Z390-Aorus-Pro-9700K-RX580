# Gigabyte Aorus Z390 Pro i7-9700K RX580
### Introduction

This repository is *exclusively* designed for Aorus Z390 Pro i7-9700K RX580 to run Catalina 10.15.X with no hassle. However, due to the time constraint, a step by step installation guide won't be covered in this guide. For more information (regarding installation), please check [[Guide] Creating OSX Installer by Rehabman](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/) or installing [Mojave 10.15.x](https://mirrors.dtops.cc/iso/MacOS/daliansky_macos/) with [Etcher](https://www.balena.io/etcher/).

![JD72vFcbaO4qmde](https://i.loli.net/2020/02/24/JD72vFcbaO4qmde.jpg)

### Specs

- **Gigabyte Aorus Z390 Pro** (M.2 Key B * 2 + PCIe 3.0 x16 * 2 + PCIe2.0 x16 \*1)
- **Intel i7-9700K** (4.9GHz GHz OC, SMBIOS **iMac19,1**)
- **OpenCore** (0.6.0)
- **Be quiet! Dark Rock Pro 4** (250w TDP, highly recommended)
- **Ballistix DDR4 3200MHz 8G * 2** (Overclocked to 3200MHz)
- **WD Black 2018/PC SN720 NVMe 1T SSD**
- **Sapphire RX 580 Nitro+ 8G** (*BruceX 5K* Apple Res 422 Master Exporting time 8s, connected to 4K monitor via a DP-mDP cable)
- **Apple BCM94360CD** Wi-Fi/Bluetooth + [M.2 NGFF Key A+E Adapter](https://www.ebay.co.uk/itm/BCM94360CS2-BCM943224PCIEBT2-12-6-Pin-WIFI-wireless-card-module-to-NGFF-M-2/223633015347?hash=item3411910233:g:clQAAOSwI7lcld~Z) 
- **USB3.1 Gen2 to PCIe Card** (ASM1142 chip-based)



### What works

- **dGPU** Hardware Accelaration  (Final Cut Pro X, VideoProc, Compressor tested)
- **AirDrop, Handoff** (Apple Wi-Fi/Bluetooth required)
- **SideCar** (with an Apple BT/Wi-FI card both wired or wireless would work)
- **iMessage** (SystemProductName, SystemSerialNumber, MLB, SystemUUID, are required) 
- **All Rear USB3.1 USB2.0 Type-C/Type-A Ports** (few USB2.0 ports disabled) 
- **HDMI Audio** (**Sound Control** is required to adjust volume)
- **Onboard HD Audio** (Realtek alc892, layout id: 1)
- **USB 3.1 Gen2** (may require a USB3.1 Gen2 to PCIe Card, i.g. ASM1142 chip-based)
- **H264/H265 HW Encode/Decode Supported**
- **Netflix (1080P) with Safari Supported** (using boot-arg `shikigva` and set `Automatioc Platform` (iMac19,1 SMBIOS not iMacPro1,1 or MacPro7,1) to `Yes`, although Apple TV+ hasn't been tested yet. I do not subscribe to it.)

### Bugs 

- Both Sidecar and DRM Content playback are supported individually, but Safari (playing DRM contents)  will turn to **Black Screen** while using Sidecar. Fortunately, there is a dummy solution for that, see pinned [issue](https://github.com/sarkrui/Hackintosh-Z390-Aorus-Pro-9700K-RX580/issues/3). 

### To-do

* Future deployment may involve a GC-Titian-Ridge Thunderbolt 3 PCIe adapter card.
* Investigation into KVM-Opencore solution to have full performace of macOS and Windows simultaneously.



<details>
<summary> Kext explained</summary>

| Name     | Note     |
| ---- | ---- |
|**AppleALC**| Fixing onboard audio |
|**CPUFriend**| CPU frequency dependency |
|**CPUFriendDataProvider**| CPU frequency profile for i7-9700K with iMac19,1 SMBIOS |
|**IntelMausi**| Fixing onboard Ethernet |
|**Lilu**| Essential Dependency |
|**SMCProcessor**| CPU status plugin |
|**SMCSuperIO**| IO port status plugin |
|**SystemProfilerMemoryFixup**| Fixing memory information in system info page |
|**USBPorts**| Defining USB ports of Aorus Z390 Pro |
|**VirtualSMC**| Sensors status dependency |
|**[WhateverGreen](https://github.com/acidanthera/WhateverGreen)**| GPU dependency (v1.3.8 later required) |

</details>



<details>
<summary> Drivers explained</summary>

|  Name    | Note      |
| ---- | ---- |
|ApfsDriverLoader.efi| Apfs volume support |
|FwRuntimeServices.efi| Fixing memory |
|HfsPlus.efi| HFS volumes support |
|[MemoryAllocation.efi](https://github.com/williambj1/OpenCore-Factory/releases/tag/OpenCore-UEFI-Drivers)| 512MB Memory allocation, mandatory for Aorus Z390 Pro |

</details>

### Deployment

<details>
<summary>1. BIOS Firmware and Settings</summary>

I would highly suggest you to upgrade you BIOS firmware to **12d** and apply the profile I created in the `/repositoryRoot/BIOS/`. This would allows you to unlock your motherboard CFG, allowing you to use native NVRAM. 

1. Clone the repository

2. Copy `EFI` and `BIOS` to your EFI formatted USB flash. (usually needs to be mounted via, for instance:
```bash
sudo diskutil mount disk2s1 (disk2s1 shall be replaced by your USB identifier)
```
3. Reboot your PC, press `F8` to enter `Q-Flash`, choose from `USB` and select `/BIOS/Z390AORP.12d`
4. (Wait util it gets done)
5. Enters BIOS setting again and load the BIOS profile: either `Profile_noCFG_noOC` (if you do not expect to overclock your CPU)
6. Reboot.

</details>

<details>
<summary>2. macOS Installation</summary>

**macOS DMG Download**: [link1](https://mirrors.dtops.cc/iso/MacOS/daliansky_macos/), [link2](http://drive.verynerd.info/macOS%20OS/), [link3](http://drive.verynerd.info/macOS%20OS/)

#### Option 1: GUI (recommended)

- [Install macOS with etcher](https://www.balena.io/etcher/)

#### Option 2: Command-line

- [[Guide] Creating OSX Installer by Rehabman](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/) 

</details>

<details>
<summary>3. Fixing iServices</summary>

To use Apple services, you need to have a vaild but unregistered serial (consisting of four attributes, shown as below) patched in your config.plist. For more editing details, please check wiki page: [How to fix iServices](https://github.com/sarkrui/Hackintosh-Z390-Aorus-Pro-9700K-RX580/wiki/How-to-fix-iServices)

```bash
Type = SystemProductName
Serial = SystemSerialNumber
Board Serial = MLB
SmUUID = SystemUUID
```

</details>


<details>
<summary> 4. Dual Boot (Windows)</summary>

Using motherboard boot menu would disrupt the deflaut booting order, to resolve so, using OpenCore to boot Windows Boot Manager is one solution. You only need to add a boot entry for your Windows Boot Manager EFI. For more details, please check wiki page: [How to add a boot entry](https://github.com/sarkrui/Hackintosh-Z390-Aorus-Pro-9700K-RX580/wiki/How-to-add-a-boot-entry-in-OpenCore)

</details>

### Changelog

#### 2020/08/09

+ Add Big Sur Beta support

#### 2020/06/13

* Add vega56/64 support

#### 2020/02/24

* Add WIKI pages
* Update README

#### 2020/02/23

* Initial release



### Credits

* **OpenCore Bootloader** from [OpenCore Respository](https://github.com/acidanthera/OpenCorePkg)
* **The best Installation guide I followed** from [OpenCore Vanilla Desktop Guide](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/)
* **CFG-locked BIOS profile** retrieved form [AudioGod's Aorus z390 Pro Guide](https://www.insanelymac.com/forum/topic/339980-audiogods-aorus-z390-pro-patched-dsdt-mini-guide-and-discussion/)
* **MemoryAllocation.efi**, which is explicitly required for addressing the Aorus Z390 Pro's memory allocation issue retrieved from [UEFI Drivers](https://github.com/williambj1/OpenCore-Factory/releases/tag/OpenCore-UEFI-Drivers) 
