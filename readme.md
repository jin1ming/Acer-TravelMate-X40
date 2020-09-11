* OpenCore: `[Solved]`
- ~~I'm having problems with OpenCore: After sleep, macOS will auto restart, but Clover works very good no problems~~
- ~~I'll fix it too late~~
* Overview
Run macOS Mojave (10.14) or Catalina (10.15) on an Acer Swift3 SF315 52G.
Most pieces are fully supported, and this setup can be used as your main machine. A few pieces are missing (and some will probably never be supported). Caveats in the Hardware section.
You can use the EFI folder to be to boot into a USB installer, or for regular booting.
** Hackintosh Mojave + Catalina Acer-Swift3-SF315-52G
  - CPU : Intel Core i5-8250U (Kabylake-R)
  - Graphics : Intel UHD 620 (+ MX150)
  - RAM : SK Hynix HMA81GS6CJR8N-VK 8 GB DDR4 2666 MHz (2 slot, maximum upgrade 32GB)
  - SSD : Hynix 256GB M.2 SOLID STATE DRIVE 80MM L (HFS256G39TND-N210A) Read : Up to 530MB/s(128/256/512GB)\ Write : Up to 190MB/s(128GB)
  - Screen : 15.6-inch 1920 x 1080 glossy IPS
  - Ports : 2 x USB 3.0, HDMI (Full Size), USB 2.0, USB Type-C, 1xAudio jack
  - Wifi/Bluetooth : Intel AC-7265, (M.2 NGFF)
  - Audio : ALC256
  - SD Card Reader : Realtek USB2.0-CRW (ven id:0bda, dev id:0129)
  - Back-lit keyboard
  - Controller (Synaptic): VoodooI2CHID, VoodooI2C + PS2Controller
** What is working
  - Graphics: Intel UHD Graphics 620 (Disable MX150)
  - Audio: Speakers, headphones (Mic don't work)
  - Keyboard: Backlight is ACPI-managed so it works just fine too
  - Trackpad: Patch I2C + VoodooI2CHID,VoodooI2C makes it buttery-smooth, supports all the macOS gestures
  - USB: Full support
  - Webcam: PhotoBooth works fine
  - Sleep/Wake: works very well
  - Wifi/Bluetooth: You need replace Broadcom BCM94352Z
** What is not working
  - Mic: I tried many ways but it not works
  - HDMI: Just works after Sleep/Wake, LOL
* Hardware
** Graphics
Integrated Intel UHD Graphics 620 support is handled by [WhateverGreen](https://github.com/acidanthera/WhateverGreen), and configured in the
`PciRoot(0x0)/Pci(0x2,0x0)` section of config.plist. The Nvidia GPU MX150 is not supported due to hardware differences and lack of driver support in macOS. It is disabled to save power.
- Recommend to use `AAPL,ig-platform-id: 0000C087` for UHD620 Kabylake-R
*** Installtion 
- config.plist
  - `Devices/Properties/PciRoot(0x0)/Pci(0x2,0x0)`
    - `AAPL,ig-platform-id: 0000C087`
    - `device-id: 16590000`
    - `disable-external-gpu: 01000000` (Disable GPU, -wegnoegpu Boot Flags or [SSDT-dGPU-Off.dsl](https://github.com/linhnguyengas/Hackintosh-Acer-Swift3-SF315-52G/blob/master/SSDT-dGPU-Off/SSDT-dGPU-Off.dsl) )
    - `enable-hdmi-dividers-fix: 01000000`
    - `enable-hdmi20: 01000000`
    - `framebuffer-con0-enable: 01000000`
    - `framebuffer-con0-pipe: 12000000`
    - `framebuffer-con1-enable: 01000000`
    - `framebuffer-con1-pipe: 12000000`
    - `framebuffer-con1-type: 00080000`
    - `framebuffer-con2-enable: 01000000`
    - `framebuffer-con2-pipe: 12000000`
    - `framebuffer-con2-type: 00080000`
    - `framebuffer-fbmem: 00009000`
    - `framebuffer-stolenmem: 00003001`
    - `framebuffer-patch-enable: 01000000`
    - `framebuffer-unifiedmem: 00000080`
    - `hda-gfx: onboard-1`
    - `model: Intel UHD Graphics 620`
To determine if Intel GPU (IGPU) acceleration is working, check: About This Mac -> Intel UHD Graphics 620
1536 MB. A value less than 1536MB indicates a problem (e.g. 7MB or 31MB are common).
** Touchpad
Acer-Swift3-SF315-52G using Touchpad Synaptic. If only using VoodooI2C and VoodooI2CHID it will not work smoothly, you need to patch [[SSDT-I2C](https://github.com/linhnguyengas/Hackintosh-Acer-Swift3-SF315-52G/blob/master/Patch%20I2C%20TouchPad/SSDT-I2C.dsl) to be able to use as a real Trackpad of Macbook 
*** Custom setting the delay between Touchpad and keyboard
- To do that you need to edit Info.plist in VoodooI2CHID.kext: Open the `Info.plist` in the VoodooI2CHID.kext with any Text Editor(I use Visual Code)
- Finding the `QuietTimeAfterTyping`
- Changing the value you prefer
- I have preset the `value` to `0`
** Wi-Fi/Bluetooth
*** Installation
- Include [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup) for Wi-Fi
- Include [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases) for Bluetooth (for installation to CLOVER/kexts/Other)
** iMessages and FaceTime
You can try this tips https://hackintosher.com/guides/quick-fixes-facetime-icloud-imessage-hackintosh-not-working/ or https://www.tonymacx86.com/threads/an-idiots-guide-to-imessage.196827/
** Battery status
Install `SMCBatteryManager.kext` that comes with [VirtualSMC](https://github.com/acidanthera/virtualsmc/releases) to get battery status. Ensure that you have removed ACPIBatteryManager if you’ve installed it previously.
** Audio 
Audio on the Acer Swift3 SF315 52G is based on the Realtek ALC256 audio codec. The ALC256 is not supported on macOS by default, so we use AppleALC to enable it. Audio pipelines on laptops appear to have unique amplifier and gain setups, so we need to pass a layout-id to AppleALC compatible with the Acer Swift3 SF315 52G. The only ID that works well is layout-id`21.
*** Installtion 
- config.plist
  - `Devices/Properties/PciRoot(0x0)/Pci(0x1f,0x3)`
    - `AAPL,slot-name: Internal`
    - `hda-gfx: onboard-1`
    - `hda-idle-support: 1`
    - `layout-id: 21`
    - `model: Intel Sunrise Point-LP HD Audio`
** Fix the AirPods choppy/unreliable when connected to macOS play music
*** Open `System Preferences > Sound > Input`
- Change Sound Input from Airpods to `Internal Microphone`
- Possible explanation:
- Since `Broadcom BCM94352Z` are running with Bluetooth 4.0, which has too low bandwidth to handle both input/output at a high quality.
- Therefore, changing `Sound` `Input` to `Internal Microphone` to ensure audio output is working as normal.
** Problem Audio after sleep/wake
- Need install [CodecCommander.kext](https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/)
- Next, we will modify the CodecCommander.kext before installing it, to make it work with our ALC256
- Right-click on CodecCommander -> Show Package Contents -> Contents -> Info.plist and open with Xcode
- Find IOKitPersonalities -> CodecCommander -> Codec Profile 
- [[https://user-images.githubusercontent.com/43808684/84402641-e5143000-ac2e-11ea-976f-88c87e5736e7.png]]
- Go to IOKitPersonalities -> CodecCommander -> Codec Profile Alter the Comment from 0x19 SET_PIN_WIDGET_CONTROL 0x25 or 0x24 to 0x19 SET_PIN_WIDGET_CONTROL 0x20 (see screenshot)
- [[https://user-images.githubusercontent.com/43808684/84402656-ec3b3e00-ac2e-11ea-98d3-bfc17ae53887.png]]
- Save!!!
- Copy CodecCommander.kext under ‘EFI -> Clover -> Kexts -> Other‘ folder and then restart the system
** USB
Has a few incorrect USB properties, but we can inject the correct properties via USBInjectAll with [SSDT-UIAC](https://github.com/RehabMan/OS-X-USB-Inject-All/blob/master/SSDT-UIAC.dsl)
* Change Key Brightness
** Very basic or patch [SSDT-PS2-KEY](https://github.com/linhnguyengas/Hackintosh-Acer-Swift3-SF315-52G/blob/master/Key%20Map%20PS2/SSDT-PS2-KEY.dsl)
- You just need to connect USB Keyboard or USB Mouse
- Go to System Preferences > Keyboard > Shortcuts > Display (if don't connect USB Keyboard or USB Mouse , Display won't appear)
[[https://user-images.githubusercontent.com/43808684/88456714-2c3a4580-ceaa-11ea-954c-ddc3f21970ce.png]]
- Change key F3, F4
[[https://user-images.githubusercontent.com/43808684/88456719-36f4da80-ceaa-11ea-8ff6-bc1fd4f55af3.png]]
- Use FN + F3 to decrease brightness, FN + F4 increase brightness
** Enable HiDPI
[One key HIDPI](https://github.com/xzhih/one-key-hidpi)
- Support 1424x802 HiDPI resolution
- The purpose of this script is to enable HiDPI options for low and medium-resolution screens and has native HiDPI settings, which can be set in the system display settings without the need for RDM software
- The DPI mechanism of macOS is different from that of windows. For example, the 1080p screen has 125% and 150% zoom options under win, while the same screen under macOS, the zoom option is simply to adjust the resolution, which makes The font and UI look small at the default resolution, and the resolution becomes blurred again.
- At the same time, this script can also repair the splash screen by injecting the patched EDID, or the splash screen problem after sleep wake up, of course, this repair varies from person to person
- In the second stage of booting, the logo will always be slightly enlarged because the resolution is counterfeit
* Guide Patch SSDT
- [https://ocbook.tlhub.cn/]
