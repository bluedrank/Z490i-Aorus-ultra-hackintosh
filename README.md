# Z490i-Aorus-ultra-hackintosh


Đây là repository chia sẻ EFI hackintosh cho **Gigabyte Z490i Aorus Ultra**. Khong hoat dong tren phien ban Catalina, minh chua tim duoc nguyen nhan.

## Phan cung

* Motherboard: Gigabyte Z490i Aorus Ultra
    * Ethernet: Intel I225-V 2.5Gbit
    * Wi-Fi/BT: Intel AX201NGW (onboard) - Disabled / BCM943602CS(M.2 Adapter)
    * Audio: Realtek ALCS1220A
* CPU: Intel i5-10400
* GPU: Intel UHD630 / AMD RX 460
* RAM: CORSAIR DOMINATOR PLATINUM DDR4 3200 32GB(16G×2)
* Display: DELL P2419H

## Phan mem

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

## Chi tiet

### GPU

#### Intel UHD630

Neu ban khong co dGPU vui long thay the gia tri nhu ben duoi

Working by:

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

Native support. Khong can chinh sua gi.

### Audio

ALC1220 audio. neu khong hoat dong ban hay thu thay the cac layout khac.
Working by:

* AppleALC.kext
* layout-id=3

DeviceProperties: 

```xml
<key>PciRoot(0x0)/Pci(0x1f,0x3)</key>
<dict>
    <key>layout-id</key>
    <integer>3</integer>
</dict>
```

### Ethernet 


Working by:

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
Them vao boot-args
```
dk.e1000=0
```

### Wi-Fi/BT

Minh da disable disable card wifi goc tren mainboard va thay the bang BCM943602CS + adapter PCIE -> M2 SSD. Neu van muon su dung card wifi goc moi nguoi them kext AirportItlwm, IntelBluetoothFirmware va map lai USB port(HS14 la port bluetooth - usb type: 255) va disable ssdt SSDT-Disable-CNVW.aml

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

Cac huong dan ban co the tim tai day[OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).

## Credits

* [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
* [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
