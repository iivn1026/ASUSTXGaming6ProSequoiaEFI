# Third-party notices / 第三方组件说明

## English

This package is a collection of machine configuration files and third-party boot components. It does not relicense those components. Included bundles retain their executable contents and plug-ins; VoodooI2C Info.plist has a documented local controller-match addition. AppleDouble/Finder metadata is excluded. Sources and the versions observed in the working EFI are listed below.

| Component | Recorded version / origin | Upstream / notice |
|---|---|---|
| OpenCore / OpenRuntime / OpenCanopy / ResetNvramEntry | DBG-109-2026-09-30 | [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), `LICENSES/OpenCorePkg.txt` |
| OpenCanopy fonts, labels and GoldenGate icons | Existing working Resources; background image excluded | [OcBinaryData](https://github.com/acidanthera/OcBinaryData); upstream resources and authorship retained |
| HfsPlus | Existing EFI binary | [OcBinaryData](https://github.com/acidanthera/OcBinaryData); retain the upstream binary's terms |
| Lilu | 1.7.3 | [Lilu](https://github.com/acidanthera/Lilu), `LICENSES/Lilu.txt` |
| AppleALC | 1.9.9 | [AppleALC](https://github.com/acidanthera/AppleALC), `LICENSES/AppleALC.txt` |
| WhateverGreen | 1.7.2 | [WhateverGreen](https://github.com/acidanthera/WhateverGreen), `LICENSES/WhateverGreen.txt` |
| VirtualSMC and SMC plug-ins | 1.3.9 | [VirtualSMC](https://github.com/acidanthera/VirtualSMC), `LICENSES/VirtualSMC.txt` |
| NVMeFix | 1.1.4 | [NVMeFix](https://github.com/acidanthera/NVMeFix), `LICENSES/NVMeFix.txt` |
| BrightnessKeys | 1.0.4 | [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys), `LICENSES/BrightnessKeys.txt` |
| RestrictEvents | 1.1.7 | [RestrictEvents](https://github.com/acidanthera/RestrictEvents), `LICENSES/RestrictEvents.txt` |
| AirportItlwm | 2.4.0 alpha; Sequoia binary 9bc4b4d and Tahoe binary bbe368e, gated by Darwin version | [laobamac/itlwm](https://github.com/laobamac/itlwm), `LICENSES/AirportItlwm.txt`; Intel firmware notice in `LICENSES/Intel-firmware.txt` |
| IntelBluetoothFirmware / IntelBTPatcher | 2.5.0 | [OpenIntelWireless](https://github.com/OpenIntelWireless/IntelBluetoothFirmware), `LICENSES/IntelBluetoothFirmware.txt`; Intel firmware notice retained |
| BlueToolFixup | 2.7.3 | [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM), `LICENSES/BrcmPatchRAM.txt` |
| TXGamingFanBridge / SSDT-TXGFAN | Local experimental ASUS WMI adapter already present in source EFI | Local bridge and ACPI configuration; limited to Darwin 24 in this config; Tahoe fan control is not claimed; no blanket license asserted |
| USBToolBox | 1.2.0 | [USBToolBox/kext](https://github.com/USBToolBox/kext); source copyright: Dhinak G, 2020–2021. A separate root license file was not found during preparation. |
| UTBMap | 1.1, machine-specific map | Codeless USBToolBox configuration from this machine |
| VoodooI2C | 2.9.1; local exact `8086:7F4F` match and timing addition | [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), `LICENSES/VoodooI2C.txt` |
| VoodooI2CHID | Bundle version 1 | Copyright © 2017 Alexandre Daoud, retained in bundle metadata; associated VoodooI2C project. Exact local build provenance remains to be documented. |
| VoodooPS2Controller | 2.3.8 | [VoodooPS2](https://github.com/acidanthera/VoodooPS2), `LICENSES/VoodooPS2.txt` |
| RealtekRTL8111 | 2.4.2 | [Mieze/RTL8111_driver_for_OS_X](https://github.com/Mieze/RTL8111_driver_for_OS_X), copyright Laura Müller, GPL v2 or later; `LICENSES/RealtekRTL8111.txt` |
| AppleMCEReporterDisabler | 1.2, `org.xlnc.disabler.MCEReporter` | Existing codeless disabler bundle; no blanket license is asserted for it |
| NullMothSafe | Configured recovery EFI tool | Distributed with the [nullmoth / 1401 project](https://github.com/nullmoth/nvidia-macos-driver); its upstream terms apply |

The full NVIDIA system driver, GPU bundles and firmware are not included. This file records available notices and remaining provenance gaps for review; it is not a claim that every component has the same license.

## 简体中文

本包由本机配置和第三方启动组件组成，不对第三方组件重新授权。保留执行文件与插件；VoodooI2C 的 Info.plist 包含已说明的本机控制器匹配及时序修改，去除 AppleDouble / Finder 元数据。上表列出了实际配置中的版本、上游来源和已取得的许可证文件。

USBToolBox 上游根目录未找到单独许可证，保留其作者版权说明；本地 VoodooI2CHID 构建的确切来源和 codeless AppleMCEReporterDisabler 的授权来源仍在清单中注明。没有把它们统一标成 MIT、BSD 或 GPL。NullMothSafe 遵循 nullmoth / 1401 上游条款。

完整 NVIDIA 系统驱动、GPU bundle 和固件不在本 EFI 包中。本文件供发布前检阅，不能当作对所有组件具有同一许可证的声明。
