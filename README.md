# Z490i-Aorus-ultra-hackintosh

Đây là repository chia sẻ EFI hackintosh cho **Gigabyte Z490i Aorus Ultra**. 

## Lưu ý:
- Thư mục EFI dành cho phiên bản MacOS 11, 12 (Bigsur / Monterey). Đối với MacOS 12 Ethernet i225v không hoạt động.
- Thư mục EFI-catalina dành cho phiên bản MacOS catalina. Tôi chưa kiểm tra nó có hoạt động với các phiên bản MacOS thấp hơn không.

## Sự khác biệt giữa 2 thư mục EFI
- Có 2 kext được thêm trong EFI-catalina: FakePCIID.kext, FakePCIID_Intel_I225-V.kext nếu không có khi boot sẽ bị kernel panic.
- EFI-catalina: UEFI -> APFS -> MinDate = -1
                UEFI -> APFS -> MinVersion = -1
    Khi đặt là 0 như thư mục EFI của phiên bản Bigsur hoặc Monterey thì sau khi hoàn tất việc cài đặt bạn sẽ không thấy partition MacOS vừa cài đặt tại boot picker. 

## Phần cứng

* Motherboard: Gigabyte Z490i Aorus Ultra
    * Ethernet: Intel I225-V 2.5Gbit
    * Wi-Fi/BT: Intel AX201NGW (onboard) - Disabled / BCM943602CS(M.2 Adapter)
    * Audio: Realtek ALCS1220A
* CPU: Intel i5-10400
* GPU: Intel UHD630 / AMD RX 460
* RAM: CORSAIR DOMINATOR PLATINUM DDR4 3200 32GB(16G×2)
* Display: DELL P2419H

## Phần mềm

* Bootloader: OpenCore 0.7.8
* OS: macOS Monterey 11.6.1 (iMac20,1)

## Hoạt động

- [x] Intel UHD630 (iGPU)
- [x] AMD RX 460 (dGPU)
- [x] Audio Realtek ALC1220
- [x] Intel I225-V 2.5Gb Ethernet
- [x] Wi-Fi/BT (BCM943602CS)
- [x] USB
- [x] Restart/Shutdown
- [x] Sleep/Wake
- [x] Power Management (Native support)

## Không Hoạt động
- [x] DRM không hoạt động trên Bigsur/Monterey

## Chi tiết

### GPU

#### Intel UHD630

Nếu bạn không có dGPU bạn hãy sửa lại khoá **ig-platform-id** như bên dưới, bạn có thể tìm thấy nó tại **DeviceProperties -> PciRoot(0x0)/Pci(0x2,0x0)**

* ig-platform-id = `07009B3E`

DeviceProperties: 

```xml
<key>PciRoot(0x0)/Pci(0x2,0x0)</key>
<dict>
    <key>AAPL,ig-platform-id</key>
    <data>BwCbPg==</data>
    <key>framebuffer-patch-enable</key>
    <data>AQAAAA==</data>
</dict>
```

#### AMD RX 460

Native support. Không cần sửa gì.

### Audio

ALC1220 audio. Nếu không hoạt động hãy thử thay thể với các layout khá: 1, 2, 3, 5, 7, 11, 13, 15, 16, 17, 21, 27, 28, 29, 30, 34, 35, 98, 99, 100
Hoạt động với:

* AppleALC.kext
* layout-id=3

### Ethernet 

Hoạt động với:

* device-id=`F2150000`

Them vao DeviceProperties: 

```xml
<key>PciRoot(0x0)/Pci(0x1C,0x4)/Pci(0x0,0x0)</key>
<dict>
    <key>device-id</key>
    <data>8hUAAA==</data>
</dict>
```

Kernel Patch:

```xml
<dict>
    <key>Base</key>
    <string>__Z18e1000_set_mac_typeP8e1000_hw</string>
    <key>Comment</key>
    <string>I225-V patch</string>
    <key>Count</key>
    <integer>1</integer>
    <key>Enabled</key>
    <true/>
    <key>Find</key>
    <data>8hUAAA==</data>
    <key>Identifier</key>
    <string>com.apple.driver.AppleIntelI210Ethernet</string>
    <key>MaxKernel</key>
    <string>20.4.0</string>
    <key>MinKernel</key>
    <string>19.0.0</string>
    <key>Replace</key>
    <data>8xUAAA==</data>
</dict>
```

boot-args
Thêm vào boot-args
```
dk.e1000=0
```

**Lưu ý:** với MacOS catalina cần thêm kext: FakePCIID.kext, FakePCIID_Intel_I225-V.kext Nếu không có khi boot sẽ bị kernel panic(chưa rõ nguyên nhân)

### Wi-Fi/BT

Tôi đã disable card wifi gốc có trên mainboard và thay thế nó bằng BCM943602CS + adapter PCIE -> M2 SSD. Nếu vẫn muốn sử dụng card wifi gốc các bạn hãy thêm kext AirportItlwm, IntelBluetoothFirmware và map lại USB port(HS14 là port bluetooth - usb type: 255) **hãy xoá hoặc disable ssdt SSDT-Disable-CNVW.aml trong ACPI**

### BIOS SETUP
#### Disable

* Fast Boot
* VT-d
* CSM
* Intel platform trust technology
* CFG Lock

#### Enable

* VT-x (no option in BIOS, it's enabled by default)
* Above 4G decoding
* Hyper-Threading
* EHCI/XHCI Hand-off
* OS type: Other
* DVMT Pre-Allocated(iGPU Memory): 64MB


### Installation

Các hướng dẫn khác bạn có thể tìm hiểu tại đây[OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).

## Credits

* [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
* [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
