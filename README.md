# ASUSTXGaming6ProSequoiaEFI

ASUS TX Gaming FX608LM — macOS Sequoia EFI

[English](#english) · [简体中文](#简体中文)

## English

### Overview

Sanitized OpenCore EFI for **ASUS TX Gaming FX608LM**. macOS Sequoia is installed on a separate SanDisk SSD; Windows remains on the original Samsung SSD.

Configuration checked on **11 October 2026**. Private identity information and unused files have been removed from the working EFI. The sanitized copy has not been boot-tested, and compatibility with other ASUS models has not been verified.

### Hardware and tested environment

| Component | Specification |
|---|---|
| Laptop | ASUS TX Gaming **FX608LM** |
| BIOS | **FX608LM.315**, recorded installation baseline |
| CPU | Intel Core Ultra 7 **255HX**, Arrow Lake-HX; **20 cores / 20 threads online** in the current macOS installation |
| Memory | **32 GB** |
| Dedicated GPU | NVIDIA GeForce **RTX 5060 Laptop GPU**, `10DE:2D59`, **8 GB VRAM** |
| Integrated GPU | Intel `8086:7D67`; disabled in this configuration |
| Internal display | **2560 × 1600**, currently reported at **60 Hz** |
| Wi-Fi / Bluetooth | Intel **AX210**, Wi-Fi `8086:2725`, Bluetooth USB `8087:0032` |
| Ethernet | Realtek **RTL8111 family**, `10EC:8168` |
| Audio | Realtek **ALC256**, `10EC:0256`, ASUS subsystem `1043:1064`; Intel SST controller `8086:7F50` |
| macOS SSD | **SanDisk Ultra 3D NVMe, 500 GB**, with a dedicated **512 MiB FAT32 EFI partition** |
| Windows SSD | **Samsung MZVL81T0HELB-00BTW, 1 TB**, retained independently |
| macOS | **Sequoia 15.8.1**, build **24H32**, Darwin **24.6.0** |
| SMBIOS model | **MacBookPro16,2** |
| OpenCore | Recorded build **DBG-109-2026-09-30**; a development/debug build |

### Device status

| Device / feature | Status and evidence |
|---|---|
| Internal SSD boot | **Working.** The running OpenCore boot path points to the SanDisk SSD's EFI partition; the installation boots without the installer USB. |
| CPU enumeration | **Working.** macOS reports 20 active physical/logical CPUs. Full power-management and stress testing remain pending. |
| NVIDIA graphics | **Working in the current installation.** The system reports RTX 5060 Laptop, 8 GB VRAM and **Metal 3**; the internal display is online. NVIDIA system drivers must be installed separately. |
| Native Wi-Fi | **Working.** AirportItlwm 2.4.0 from the laobamac fork provides the native Wi-Fi interface; the active default route uses that interface. |
| Internal speakers | **Working.** Speaker playback is confirmed with AppleALC and `alcid=97`. |
| Headphone output | **Working.** Headphone playback is confirmed; the headset microphone is untested. |
| Internal microphone | **Working.** Internal microphone recording is confirmed. |
| Line input | **Detected; recording not tested.** The device appears in macOS audio information. |
| USB | **Mapped and partially tested.** USB 2.0/3.0 port objects are present; external USB keyboard/mouse and the internal camera enumerate. Every physical port, USB-C orientation and transfer speed still need individual tests. |
| Keyboard / external mouse | Internal PS/2 keyboard and external USB HID devices are detected; special keys are not fully tested. |
| Battery | **Status reporting works.** macOS reports the internal battery, charge level and AC power state. Battery endurance and sleep behavior are untested. |
| Ethernet | **Driver loaded; cable test pending.** RTL8111 is present as an interface, but wired connectivity has not been verified. |
| Bluetooth | **Not working in this configuration.** Intel Bluetooth hardware is detected over USB, but its firmware/patch/fixup kexts are disabled. |
| Touchpad | **Not working.** The included input kexts do not provide a working touchpad. |
| Sleep / wake, sensors, HDMI audio, Apple services | **Not fully verified.** Functional testing for these features is incomplete. |

### Known issues

1. **Intermittent screen flicker / flashing during startup.** The screen sometimes flickers or flashes while booting. This bug is **unresolved**; its exact timing and cause have not been isolated. The display works after a successful boot, but the startup display transition is not always smooth.
2. **Touchpad does not work.** ACPI, controller and device binding require further investigation.
3. **Bluetooth is unavailable.** The Intel Bluetooth kexts are disabled and excluded from this minimal package.
4. **Startup stability is still under observation.** Intermittent boot failures occurred during bring-up. Reset NVRAM did not consistently solve them. Long-term warm/cold boot reliability remains unverified; a recovery USB is recommended.
5. The Wi-Fi driver is an **alpha** build. Its upstream documentation lists limitations including WPA3-only networks and AWDL/AirDrop. These features are not verified on this machine.

### Configuration notes

- The **20-core configuration** uses machine-specific `DSDT-FX608LM-20C.aml` and `APIC-FX608LM-20C.aml`. The early diagnostic patch that forced the CPU count to eight is disabled and omitted; `ProvideCurrentCpuInfo=true` is retained.
- Speaker playback, headphone output and internal microphone recording are confirmed with **`alcid=97`**. Layout 97 is retained from the original configuration; the headset microphone and line input remain untested. Do not remove this parameter merely because the system-visible `layout-id` differs.
- **AirportItlwm** provides native Wi-Fi. The Ethernet-style `itlwm` + HeliPort setup is no longer used. The included binary matches the tested Sequoia Release build at commit **`9bc4b4d`**. The mutable alpha release tag may later point to different assets. Do not enable `itlwm` and AirportItlwm together.
- This EFI was exported **after NVIDIA driver installation**, with `ResizeAppleGpuBars=-1` and `ResizeGpuBars=13` retained. EFI files alone do not install the NVIDIA driver, its auxiliary kernel collection, GPU bundles or firmware.
- The iGPU is disabled and the dedicated GPU is used. macOS driver support for the Intel integrated GPU has not been demonstrated.
- Third-party kext security settings and diagnostic boot arguments are inherited from the working installation. Review them before use. A Reset NVRAM menu entry is included and can alter firmware boot entries.

### Privacy and package contents

**The serial number, MLB, UUID and ROM have been replaced with newly generated demonstration values. Replace all four locally before using Apple services. Do not reuse the public identity set.** The SMBIOS model remains MacBookPro16,2. These values protect private identity information; they do not prove Apple service activation or hardware authenticity.

The package contains only the following files:

```text
EFI/
  BOOT/BOOTx64.efi
  OC/
    OpenCore.efi
    config.plist
    ACPI/       # enabled machine-specific tables
    Drivers/    # HfsPlus, OpenRuntime, ResetNvramEntry
    Kexts/      # enabled bundles, with their original plug-ins/resources
    Tools/      # the configured NVIDIA recovery tool
LICENSES/       # available upstream license notices
README.md
THIRD_PARTY_NOTICES.md
SHA256SUMS.txt
.gitignore
```

Excluded files and data include real serials and MAC/ROM values, Wi-Fi hotspot settings, stale Bluetooth controller data, old configs/kext backups, boot logs, screenshots, local scripts and disk images. Unused OpenCanopy images/fonts/audio are omitted because this configuration uses the **Builtin** picker with audio assist disabled. No NVIDIA files from `/Library/Extensions`, `/Library/GPUBundles` or firmware directories are redistributed in this EFI package.

Enabled file references and ACPI checksums have been checked. The exported config passes **ocvalidate 1.0.8** structure checks; this is not a substitute for validation with the exact recorded development build or for a boot test. Three missing driver `HideVerbose` fields were filled with their default value, `false`; the working EFI was not changed.

### Before using this package

1. Verify the exact laptop, BIOS, CPU topology and hardware. Keep a backup of a known-working EFI and a recovery USB.
2. Replace the demonstration SMBIOS identity and ROM privately; inspect the machine-specific DSDT and USB mapping rather than assuming portability.
3. Obtain the NVIDIA driver from its upstream project and follow the instructions for the chosen release. This package captures an already-installed driver state.
4. Identify the **macOS SSD's EFI partition by its device/model**, then copy the EFI folder there. Do not use a fixed `diskXsY` number or overwrite the Windows EFI.
5. Test boot, speakers/headphones, Wi-Fi, USB ports and display behavior before changing more settings. Further BIOS/core changes or OS upgrades require revalidation.

### Upstream projects

- [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), [OcBinaryData](https://github.com/acidanthera/OcBinaryData)
- [Lilu](https://github.com/acidanthera/Lilu), [AppleALC](https://github.com/acidanthera/AppleALC), [WhateverGreen](https://github.com/acidanthera/WhateverGreen)
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC), [NVMeFix](https://github.com/acidanthera/NVMeFix), [RestrictEvents](https://github.com/acidanthera/RestrictEvents), [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys)
- [laobamac/itlwm — Sequoia AirportItlwm](https://github.com/laobamac/itlwm#airportitlwm-on-macos-sequoia)
- [USBToolBox](https://github.com/USBToolBox/kext), [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), [VoodooPS2](https://github.com/acidanthera/VoodooPS2)
- [Realtek RTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X)
- [nullmoth NVIDIA macOS driver](https://github.com/nullmoth/nvidia-macos-driver) — installed separately

Third-party authorship and upstream licenses are retained. See `LICENSES/` and `THIRD_PARTY_NOTICES.md`. No blanket license is applied to the combined package.

---

## 简体中文

### 项目说明

适用于 **华硕 TX Gaming FX608LM** 的脱敏 OpenCore EFI。macOS Sequoia 安装在独立的 SanDisk SSD 上，原 Samsung SSD 上的 Windows 单独保留。

配置检查日期为 **2026 年 10 月 11 日**。分享前已移除私人身份信息和冗余文件。脱敏后的副本尚未重新进行启动测试，也未验证对其他华硕机型的兼容性。

### 硬件与测试环境

| 项目 | 信息 |
|---|---|
| 机型 | 华硕 TX Gaming **FX608LM** |
| BIOS | **FX608LM.315**，安装阶段记录的基线版本 |
| CPU | Intel Core Ultra 7 **255HX**，Arrow Lake-HX；当前 macOS 已上线 **20 核 / 20 线程** |
| 内存 | **32GB** |
| 独显 | NVIDIA GeForce **RTX 5060 Laptop GPU**，`10DE:2D59`，**8GB 显存** |
| 核显 | Intel `8086:7D67`；本配置中禁用 |
| 内屏 | **2560 × 1600**，当前系统报告 **60Hz** |
| 无线 / 蓝牙 | Intel **AX210**，无线 `8086:2725`，蓝牙 USB `8087:0032` |
| 有线网卡 | Realtek **RTL8111 系列**，`10EC:8168` |
| 声卡 | Realtek **ALC256**，`10EC:0256`，华硕子系统 `1043:1064`；Intel SST 控制器 `8086:7F50` |
| macOS 硬盘 | **SanDisk Ultra 3D NVMe 500GB**，独立 **512MiB FAT32 EFI 分区** |
| Windows 硬盘 | **Samsung MZVL81T0HELB-00BTW 1TB**，单独保留 |
| 系统 | **macOS Sequoia 15.8.1**，Build **24H32**，Darwin **24.6.0** |
| SMBIOS 机型 | **MacBookPro16,2** |
| OpenCore | 实测构建标识 **DBG-109-2026-09-30**，开发 / 调试构建 |

### 设备状态

| 设备 / 功能 | 当前状态与依据 |
|---|---|
| 内置 SSD 启动 | **可用。** 当前 OpenCore 启动路径指向 SanDisk 内置 EFI，已能脱离安装 U 盘启动。 |
| CPU 枚举 | **可用。** 系统实测 20 个物理 / 逻辑核心在线；完整电源管理和压力测试尚未完成。 |
| NVIDIA 显卡 | **当前系统中可用。** 识别 RTX 5060 Laptop、8GB 显存和 **Metal 3**，内屏在线；NVIDIA 系统驱动需要另外安装。 |
| 原生 Wi-Fi | **可用。** laobamac 分支的 AirportItlwm 2.4.0 已建立系统 Wi-Fi 接口，当前默认网络出口使用该接口。 |
| 内置扬声器 | **可用。** 扬声器播放已确认可用，使用 AppleALC 和 `alcid=97`。 |
| 耳机输出 | **可用。** 耳机输出已确认可用；耳麦麦克风尚未测试。 |
| 内置麦克风 | **可用。** 内置麦克风录音已确认可用。 |
| 线路输入 | **已识别，录音待测试。** 系统音频信息中已有设备。 |
| USB | **已映射，部分已测试。** 可见 USB 2.0 / 3.0 端口，外接键鼠和内置摄像头已枚举；仍需逐个实测接口、USB-C 正反插和传输速度。 |
| 键盘 / 外接鼠标 | 内置 PS/2 键盘和外接 USB HID 设备已识别；特殊功能键尚未全部验证。 |
| 电池 | **状态读取可用。** 系统能报告电量、内置电池和交流电供电状态；续航和睡眠表现未验证。 |
| 有线网卡 | **驱动已加载，网线实测待完成。** 已有 RTL8111 接口，未确认有线联网。 |
| 蓝牙 | **当前不可用。** Intel 蓝牙 USB 硬件已识别，但固件、补丁和修复 kext 均禁用。 |
| 触控板 | **未驱动、不可用。** 现有输入驱动未能使触控板正常工作。 |
| 睡眠唤醒、传感器、HDMI 音频、Apple 服务 | **未完整验证。** 这些功能尚未完成实际测试。 |

### 已知问题

1. **开机时偶发闪屏。** 启动过程中偶发屏幕闪烁或闪屏，问题**尚未修复**，具体发生时机和根因尚未完全定位。成功进入桌面后显示可用，但启动时的显示切换并不总是正常。
2. **触控板无法驱动。** 触控板目前不可用，ACPI、控制器和设备绑定仍需排查。
3. **蓝牙不可用。** 相关 Intel 蓝牙 kext 已禁用，精简包未包含这些文件。
4. **开机稳定性仍在观察。** 调试阶段出现过偶发启动失败，Reset NVRAM 未能稳定解决问题。长期冷启动 / 热重启稳定性尚未验证，建议保留回退 U 盘。
5. Wi-Fi 驱动为 **alpha** 构建。上游注明纯 WPA3、AWDL / AirDrop 等存在功能限制，这些功能尚未在本机验证。

### 配置要点

- **20 核配置**包含本机专用的 `DSDT-FX608LM-20C.aml` 和 `APIC-FX608LM-20C.aml`。早期强制返回 8 核的诊断补丁已禁用并移除，保留 `ProvideCurrentCpuInfo=true`。
- 使用 **`alcid=97`**，扬声器播放、耳机输出及内置麦克风录音均已确认可用。布局 97 沿用旧配置，耳麦麦克风和线路输入尚未测试。系统可见的 `layout-id` 与它不同，不是直接删除该参数的依据。
- **AirportItlwm** 提供原生无线接口，不再使用以太网形式的 `itlwm + HeliPort`。随包二进制与已测试的 Sequoia Release 构建 **`9bc4b4d`** 一致；alpha 发布标签以后可能更新到其他构建。不要同时启用 itlwm 和 AirportItlwm。
- 这套 EFI 导出于 **NVIDIA 驱动安装完成后**，保留 `ResizeAppleGpuBars=-1`、`ResizeGpuBars=13`。只复制 EFI 并不会安装 NVIDIA 系统驱动、辅助内核集合、GPU bundle 或固件。
- 核显已禁用，目前使用独显；Intel 核显的 macOS 驱动支持尚未验证。
- 第三方 kext 所需安全设置和诊断启动参数沿用当前配置，使用前应检查。包中保留 Reset NVRAM 菜单，它可能改变固件启动项。

### 脱敏与文件范围

**序列号、MLB、UUID 和 ROM 已替换成新生成的演示值。用于 Apple 服务之前，必须在本机重新生成并替换这四项，不要共用公开身份。** SMBIOS 机型仍为 MacBookPro16,2。这些演示值用于保护私人身份信息，不代表 Apple 服务已激活或具有真实设备身份。

包中仅保留启动器、启用的 ACPI 表、UEFI 驱动、kext bundle、配置文件和已配置的 NVIDIA 回退工具，另附 README、上游许可证声明、校验清单和 `.gitignore`。启用的 kext 保留原有插件 / 资源，以维护 bundle 完整性。

已移除真实三码、MAC / ROM、热点配置、旧蓝牙控制器状态、历史配置与 kext 备份、启动日志、截图、本地脚本和磁盘镜像。当前使用 **Builtin** 菜单并关闭音频辅助，因此不附带未使用的 OpenCanopy 图片、字体和语音资源。EFI 包不重新分发系统目录中的 NVIDIA 驱动、GPU bundle 和固件。

所有启用文件路径和 ACPI 校验和已检查。导出的配置通过 **ocvalidate 1.0.8** 结构检查；它不能替代对实际开发构建的验证和重新启动测试。三个 UEFI 驱动缺失的 `HideVerbose` 字段在导出副本中补为默认值 `false`，正在使用的 EFI 没有改动。

### 使用前

1. 核对确切机型、BIOS、核心拓扑和硬件，备份可用 EFI，并保留回退 U 盘。
2. 在本机替换演示身份和 ROM；检查本机 DSDT 和 USB 映射，不假设它们适用于其他电脑。
3. 从 NVIDIA 上游项目取得驱动，遵循对应版本安装步骤。本包记录的是已装好驱动的状态。
4. 按设备型号确认 **macOS 硬盘的 EFI 分区** 后复制；不要套用固定 `diskXsY`，不要覆盖 Windows EFI。
5. 先验证启动、扬声器 / 耳机、无线、USB 和显示表现，再继续调整。更改 BIOS 核心设置或升级系统后需要重新验证。

### 上游与致谢

使用 [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)、[OcBinaryData](https://github.com/acidanthera/OcBinaryData)、[Lilu](https://github.com/acidanthera/Lilu)、[AppleALC](https://github.com/acidanthera/AppleALC)、[WhateverGreen](https://github.com/acidanthera/WhateverGreen)、[VirtualSMC](https://github.com/acidanthera/VirtualSMC)、[NVMeFix](https://github.com/acidanthera/NVMeFix)、[RestrictEvents](https://github.com/acidanthera/RestrictEvents)、[BrightnessKeys](https://github.com/acidanthera/BrightnessKeys)、[laobamac/itlwm](https://github.com/laobamac/itlwm)、[USBToolBox](https://github.com/USBToolBox/kext)、[VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)、[VoodooPS2](https://github.com/acidanthera/VoodooPS2) 和 [Realtek RTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X)。[nullmoth NVIDIA 驱动](https://github.com/nullmoth/nvidia-macos-driver)单独安装。

第三方组件保留作者署名和上游许可证，见 `LICENSES/` 和 `THIRD_PARTY_NOTICES.md`。组合包未对这些组件统一重新授权。
