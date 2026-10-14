# ASUSTXGaming6ProSequoiaEFI

ASUS TX Gaming FX608LM — macOS Sequoia EFI with OpenCanopy GUI

[English](#english) · [简体中文](#简体中文)

## English

### Overview

Sanitized OpenCore EFI for **ASUS TX Gaming FX608LM**. macOS Sequoia is installed on a separate SanDisk SSD; Windows remains on the original Samsung SSD.

Configuration checked on **14 October 2026**, after reboot verification of touchpad and Bluetooth repairs. Private identity information and unused files have been removed from the working EFI. The sanitized copy has not been boot-tested, and compatibility with other ASUS models has not been verified.

### Hardware and tested environment

| Component | Specification |
|---|---|
| Laptop | ASUS TX Gaming **FX608LM** |
| BIOS | **FX608LM.315**, recorded installation baseline |
| CPU | Intel Core Ultra 7 **255HX**, Arrow Lake-HX; **20 cores / 20 threads online** in the current macOS installation |
| Memory | **32 GB** |
| Dedicated GPU | NVIDIA GeForce **RTX 5060 Laptop GPU**, `10DE:2D59`, **8 GB VRAM** |
| Integrated GPU | Intel `8086:7D67`; disabled in this configuration |
| Internal display | **16-inch, 2560 × 1600**, currently reported at **165 Hz** |
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
| NVIDIA graphics | **Display output works.** The system reports RTX 5060 Laptop, 8 GB VRAM and **Metal 3**, and Metal device enumeration succeeds. Application compatibility varies; an earlier Chrome 155 test used software rendering, and acceleration has not been rechecked after subsequent NVIDIA driver updates. NVIDIA system drivers must be installed separately. |
| Native Wi-Fi | **Working.** AirportItlwm 2.4.0 from the laobamac fork provides the native Wi-Fi interface; the active default route uses that interface. |
| Internal speakers | **Working.** Speaker playback is confirmed with AppleALC and `alcid=97`. |
| Headphone output | **Working.** Headphone playback is confirmed; the headset microphone is untested. |
| Internal microphone | **Working.** Internal microphone recording is confirmed. |
| Line input | **Detected; recording not tested.** The device appears in macOS audio information. |
| USB | **Mapped and partially tested.** USB 2.0/3.0 port objects are present; external USB keyboard/mouse and the internal camera enumerate. Every physical port, USB-C orientation and transfer speed still need individual tests. |
| Keyboard / external mouse | Internal PS/2 keyboard and external USB HID devices are detected; special keys are not fully tested. |
| Battery | **Status reporting works.** macOS reports the internal battery, charge level and AC power state. Battery endurance and sleep behavior are untested. |
| Ethernet | **Driver loaded; cable test pending.** RTL8111 is present as an interface, but wired connectivity has not been verified. |
| Bluetooth | **Working after reboot.** IntelBluetoothFirmware 2.5.0, IntelBTPatcher 2.5.0 and BlueToolFixup 2.7.3 are enabled; operation confirmed on this machine. Full Continuity compatibility is not claimed. |
| Touchpad | **Working after reboot.** ASCE1200 I2C-HID binds to VoodooI2CHID and its precision touchpad event driver, using an exact `8086:7F4F` I2C3 controller match and polling mode. Full gesture and sleep/wake testing remain incomplete. |
| Sleep / wake, sensors, HDMI audio, Apple services | **Not fully verified.** Functional testing for these features is incomplete. |

### Known issues

1. **Intermittent screen flicker / flashing during startup.** The screen sometimes flickers or flashes while booting. This bug is **unresolved**; its exact timing and cause have not been isolated. The display works after a successful boot, but the startup display transition is not always smooth.
2. **HiDPI scaling has not been achieved in the tested setup.** Native 2560 × 1600 remains active; a physical-size override was not consumed and was removed. This EFI does not include that override or a verified 160% scaling solution.
3. **Startup stability is still under observation.** Intermittent boot failures occurred during bring-up. Reset NVRAM did not consistently solve them. Long-term warm/cold boot reliability remains unverified; a recovery USB is recommended.
4. The Wi-Fi driver is an **alpha** build. Its upstream documentation lists limitations including WPA3-only networks and AWDL/AirDrop. These features are not verified on this machine.
5. **An earlier tested Chrome/driver combination did not provide Chrome hardware acceleration.** Chrome 155 reports software rendering after EGL/ANGLE GPU initialization failures. Metal device enumeration succeeds; the system reporting Metal support does not establish acceleration compatibility for every application.

### Touchpad and Bluetooth repair details

- **Touchpad:** `VoodooI2C.kext/Contents/Info.plist` adds `VoodooI2CPCIArrowLakeHX_I2C3`, matching only `0x7f4f8086`. Timing: 133 MHz I2C clock, 42 ns SDA hold, 171 ns SDA fall and 208 ns SCL fall, following the matching controller in the [Linux LPSS source](https://github.com/torvalds/linux/blob/master/drivers/mfd/intel-lpss-pci.c). Existing `-vi2c-force-polling`, XOSI and ACPI are retained. GPIO interrupt support is not claimed.
- **Bluetooth:** the existing firmware, patcher and fixup bundles are enabled. `bluetoothInternalControllerInfo` is **14 zero bytes**; `bluetoothExternalDongleFailed` is **one zero byte**, with the corresponding NVRAM Delete entries, as documented by [BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM#bluetoolfixupkext). No IntelBluetoothInjector is included.
- **After reboot:** AirportItlwm 2.4.0 still loads, native Wi-Fi is on, the default route uses `en1`, HTTPS connectivity succeeds, and the touchpad remains bound. The Bluetooth repair did not change Wi-Fi, USB mapping, touchpad, audio, ACPI or graphics-driver files.
- **Existing fan bridge:** `TXGamingFanBridge.kext` and `SSDT-TXGFAN.aml` were already present in the working source EFI and are retained. Fan telemetry/profile control remain experimental, not fully verified features. The separate fan-control app is not included.

### Graphical boot menu

- **OpenCanopy**, using the same **DBG-109-2026-09-30** build as OpenCore, with **Acidanthera\GoldenGate** icons.
- **Plain black background; no wallpaper.** The optional background image is omitted; `DefaultBackgroundColor=00000000` is retained.
- The local configuration arranges **macOS first and Windows second**, with macOS selected by default and a **5-second** timeout. The exported demonstration configuration does not contain the machine's saved NVRAM boot selection.
- **Space** reveals Recovery, Reset NVRAM and the NVIDIA recovery tool. `HideAuxiliary=true`; the recovery tool is marked auxiliary. Arrow keys and Enter select a system.
- The Windows entry in the public config is a **disabled template** with a zero partition UUID. Its real SSD device path has been removed. On a Windows EFI without the visibility markers below, native Windows scanning remains available and its position follows firmware order. For automatic scanning, remove markers left by an earlier custom-entry setup; otherwise Windows can remain hidden while the template is disabled.

To reproduce the local two-system ordering:

1. Obtain the target Windows loader's complete UEFI device path from an OpenCore debug log. Replace the placeholder `Misc → Entries → Windows → Path` with that path and enable the entry.
2. Only after the custom path is configured, copy the two `.contentVisibility` files under `Extras/Windows-visibility/EFI/` to their corresponding locations on the **Windows EFI partition**. They suppress the duplicate automatic Windows entry and make a fallback EFI entry auxiliary **only for the `TXGaming` OpenCore instance**. The original Windows bootloader is not included or replaced.
3. Keep `InstanceIdentifier=TXGaming` and `HideAuxiliary=true`. Native macOS stays before the custom Windows entry when auxiliary entries are hidden.
4. In the OC menu, select macOS and press **Ctrl + Enter** once to save it as the default. Put the internal SSD's OpenCore entry first in the firmware boot priority to enter OC at power-on.

The GUI and exported config pass static validation. The sanitized public copy has not been boot-tested.

### Configuration notes

- **Verbose boot logging (`-v`) is retained in the shared configuration.**
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
    Drivers/    # HfsPlus, OpenRuntime, OpenCanopy, ResetNvramEntry
    Kexts/      # enabled bundles, with their plug-ins/resources and the documented local I2C match
    Tools/      # the configured NVIDIA recovery tool (hidden until Space)
    Resources/  # active icons, labels and bitmap fonts; no wallpaper
Extras/Windows-visibility/  # optional OC-specific visibility markers
LICENSES/       # available upstream license notices
README.md
THIRD_PARTY_NOTICES.md
SHA256SUMS.txt
.gitignore
```

Excluded files and data include real serials and MAC/ROM values, Wi-Fi hotspot settings, private Bluetooth addresses/controller data, old configs/kext backups, boot logs, screenshots, local scripts and disk images. OpenCanopy icons, labels and bitmap fonts are included for the active GoldenGate theme. Wallpaper, audio-assist resources and unused text fonts are omitted. The Windows disk path is replaced with a disabled template; saved NVRAM boot choices are not distributed. No NVIDIA files from `/Library/Extensions`, `/Library/GPUBundles` or firmware directories are redistributed in this EFI package.

Enabled file references, ACPI checksums and required OpenCanopy resources have been checked. The exported config passes **ocvalidate 1.0.9 from the exact same OpenCore build**. Core OpenCore binaries and enabled hardware kexts are preserved; identity and disk-path redaction is applied only to the public copy. Validation does not replace an actual boot test.

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
- [IntelBluetoothFirmware / IntelBTPatcher](https://github.com/OpenIntelWireless/IntelBluetoothFirmware), [BlueToolFixup](https://github.com/acidanthera/BrcmPatchRAM)
- [nullmoth NVIDIA macOS driver](https://github.com/nullmoth/nvidia-macos-driver) — installed separately

Third-party authorship and upstream licenses are retained. See `LICENSES/` and `THIRD_PARTY_NOTICES.md`. No blanket license is applied to the combined package.

---

## 简体中文

### 项目说明

适用于 **华硕 TX Gaming FX608LM** 的脱敏 OpenCore EFI。macOS Sequoia 安装在独立的 SanDisk SSD 上，原 Samsung SSD 上的 Windows 单独保留。

配置检查日期为 **2026 年 10 月 14 日**，触控板与蓝牙修复均已完成重启验证。分享前已移除私人身份信息和冗余文件。脱敏后的副本尚未重新进行启动测试，也未验证对其他华硕机型的兼容性。

### 硬件与测试环境

| 项目 | 信息 |
|---|---|
| 机型 | 华硕 TX Gaming **FX608LM** |
| BIOS | **FX608LM.315**，安装阶段记录的基线版本 |
| CPU | Intel Core Ultra 7 **255HX**，Arrow Lake-HX；当前 macOS 已上线 **20 核 / 20 线程** |
| 内存 | **32GB** |
| 独显 | NVIDIA GeForce **RTX 5060 Laptop GPU**，`10DE:2D59`，**8GB 显存** |
| 核显 | Intel `8086:7D67`；本配置中禁用 |
| 内屏 | **16 英寸、2560 × 1600**，当前系统报告 **165Hz** |
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
| NVIDIA 显卡 | **显示输出可用。** 识别 RTX 5060 Laptop、8GB 显存和 **Metal 3**，Metal 设备枚举成功。应用加速兼容性因应用而异；此前 Chrome 155 测试使用软件渲染，后续 NVIDIA 驱动更新后的应用加速尚未重新核对。NVIDIA 系统驱动需要另外安装。 |
| 原生 Wi-Fi | **可用。** laobamac 分支的 AirportItlwm 2.4.0 已建立系统 Wi-Fi 接口，当前默认网络出口使用该接口。 |
| 内置扬声器 | **可用。** 扬声器播放已确认可用，使用 AppleALC 和 `alcid=97`。 |
| 耳机输出 | **可用。** 耳机输出已确认可用；耳麦麦克风尚未测试。 |
| 内置麦克风 | **可用。** 内置麦克风录音已确认可用。 |
| 线路输入 | **已识别，录音待测试。** 系统音频信息中已有设备。 |
| USB | **已映射，部分已测试。** 可见 USB 2.0 / 3.0 端口，外接键鼠和内置摄像头已枚举；仍需逐个实测接口、USB-C 正反插和传输速度。 |
| 键盘 / 外接鼠标 | 内置 PS/2 键盘和外接 USB HID 设备已识别；特殊功能键尚未全部验证。 |
| 电池 | **状态读取可用。** 系统能报告电量、内置电池和交流电供电状态；续航和睡眠表现未验证。 |
| 有线网卡 | **驱动已加载，网线实测待完成。** 已有 RTL8111 接口，未确认有线联网。 |
| 蓝牙 | **重启后可用。** 使用 IntelBluetoothFirmware 2.5.0、IntelBTPatcher 2.5.0 和 BlueToolFixup 2.7.3，已在本机确认生效；不代表完整连续互通功能可用。 |
| 触控板 | **重启后可用。** ASCE1200 I2C-HID 已绑定 VoodooI2CHID 和精密触控板事件驱动，使用 I2C3 控制器 `8086:7F4F` 精确匹配和轮询模式；全部手势及睡眠唤醒尚未完整测试。 |
| 睡眠唤醒、传感器、HDMI 音频、Apple 服务 | **未完整验证。** 这些功能尚未完成实际测试。 |

### 已知问题

1. **开机时偶发闪屏。** 启动过程中偶发屏幕闪烁或闪屏，问题**尚未修复**，具体发生时机和根因尚未完全定位。成功进入桌面后显示可用，但启动时的显示切换并不总是正常。
2. **已测试配置尚未实现 HiDPI 缩放。** 内屏仍使用原生 2560 × 1600；物理尺寸覆盖未被系统采用，已移除。本 EFI 不包含该覆盖或已验证的 160% 缩放方案。
3. **开机稳定性仍在观察。** 调试阶段出现过偶发启动失败，Reset NVRAM 未能稳定解决问题。长期冷启动 / 热重启稳定性尚未验证，建议保留回退 U 盘。
4. Wi-Fi 驱动为 **alpha** 构建。上游注明纯 WPA3、AWDL / AirDrop 等存在功能限制，这些功能尚未在本机验证。
5. **此前测试的 Chrome 与驱动组合无法使用 Chrome 硬件加速。** Chrome 155 因 EGL / ANGLE GPU 初始化失败而使用软件渲染。Metal 设备枚举成功，但系统报告支持 Metal，不代表所有应用都能正常使用硬件加速。

### 触控板与蓝牙修复细节

- **触控板：** 在 `VoodooI2C.kext/Contents/Info.plist` 中增加 `VoodooI2CPCIArrowLakeHX_I2C3`，仅匹配 `0x7f4f8086`。时序为 133MHz I2C 时钟、42ns SDA hold、171ns SDA fall、208ns SCL fall，依据 [Linux LPSS 源码](https://github.com/torvalds/linux/blob/master/drivers/mfd/intel-lpss-pci.c) 中该控制器的配置。保留 `-vi2c-force-polling`、XOSI 和现有 ACPI；不宣称 GPIO 中断模式已支持。
- **蓝牙：** 启用已有固件、补丁和修复 bundle。按 [BlueToolFixup 官方说明](https://github.com/acidanthera/BrcmPatchRAM#bluetoolfixupkext) 保留 `bluetoothInternalControllerInfo` 为 **14 字节全零**，`bluetoothExternalDongleFailed` 为 **1 字节全零**及对应 NVRAM Delete 项；不包含 IntelBluetoothInjector。
- **重启核对：** AirportItlwm 2.4.0 仍加载，原生 Wi-Fi 开启，默认路由为 `en1`，HTTPS 联网测试通过，触控板仍保持绑定。蓝牙修复未改动 Wi-Fi、USB 映射、触控板、声卡、ACPI 和显卡驱动文件。
- **已有风扇桥接：** 可用源 EFI 中已有 `TXGamingFanBridge.kext` 和 `SSDT-TXGFAN.aml`，本次保留。风扇读数及固件模式控制属于实验功能，不列为已完整验证的设备；单独的风扇助手应用不包含在此仓库中。

### 图形启动菜单

- 使用 **OpenCanopy**，与 OpenCore 同属 **DBG-109-2026-09-30** 构建，主题为 **Acidanthera\GoldenGate**。
- **纯黑背景，无壁纸。** 不包含可选背景图片，保留 `DefaultBackgroundColor=00000000`。
- 本地配置为 **macOS 第一位、Windows 第二位**，默认 macOS，等待 **5 秒**后启动。公开演示配置不包含本机 NVRAM 中保存的默认启动选择。
- 按**空格**显示恢复分区、Reset NVRAM 和 NVIDIA 回退工具。启用 `HideAuxiliary=true`，回退工具标为辅助项目。方向键选择，回车启动。
- 公开配置中的 Windows 项为**默认禁用的模板**，分区 UUID 使用全零占位符，真实 SSD 路径已脱敏。Windows EFI 未放置下述显示标记时，可由 OC 自动扫描 Windows，但顺序取决于固件。若改回自动扫描，应移除旧自定义启动配置留下的显示标记，否则模板禁用时 Windows 可能仍被隐藏。

复现本地双系统排列的方法：

1. 从 OC 调试日志取得目标 Windows 启动器的完整 UEFI 设备路径，替换 `Misc → Entries → Windows → Path` 并启用该项目。
2. 确认自定义路径配置完成后，将 `Extras/Windows-visibility/EFI/` 下的两个 `.contentVisibility` 文件复制到 **Windows EFI 分区**对应位置。它们仅对 `TXGaming` 这一 OC 实例隐藏重复的自动 Windows 项，并把备用 EFI 项设为辅助项目；不包含或替换 Windows 原启动器。
3. 保留 `InstanceIdentifier=TXGaming` 和 `HideAuxiliary=true`。隐藏辅助项目时，原生 macOS 项排在自定义 Windows 项之前。
4. 在 OC 菜单中选中 macOS，按一次 **Ctrl + Enter** 保存默认选择。在 BIOS 中将内置 SSD 的 OpenCore 项放在启动优先级第一位，即可开机先进入 OC。

图形菜单配置及导出副本已通过静态校验；脱敏公开副本尚未进行启动测试。

### 配置要点

- **公开配置保留 `-v` 啰嗦启动日志参数。**
- **20 核配置**包含本机专用的 `DSDT-FX608LM-20C.aml` 和 `APIC-FX608LM-20C.aml`。早期强制返回 8 核的诊断补丁已禁用并移除，保留 `ProvideCurrentCpuInfo=true`。
- 使用 **`alcid=97`**，扬声器播放、耳机输出及内置麦克风录音均已确认可用。布局 97 沿用旧配置，耳麦麦克风和线路输入尚未测试。系统可见的 `layout-id` 与它不同，不是直接删除该参数的依据。
- **AirportItlwm** 提供原生无线接口，不再使用以太网形式的 `itlwm + HeliPort`。随包二进制与已测试的 Sequoia Release 构建 **`9bc4b4d`** 一致；alpha 发布标签以后可能更新到其他构建。不要同时启用 itlwm 和 AirportItlwm。
- 这套 EFI 导出于 **NVIDIA 驱动安装完成后**，保留 `ResizeAppleGpuBars=-1`、`ResizeGpuBars=13`。只复制 EFI 并不会安装 NVIDIA 系统驱动、辅助内核集合、GPU bundle 或固件。
- 核显已禁用，目前使用独显；Intel 核显的 macOS 驱动支持尚未验证。
- 第三方 kext 所需安全设置和诊断启动参数沿用当前配置，使用前应检查。包中保留 Reset NVRAM 菜单，它可能改变固件启动项。

### 脱敏与文件范围

**序列号、MLB、UUID 和 ROM 已替换成新生成的演示值。用于 Apple 服务之前，必须在本机重新生成并替换这四项，不要共用公开身份。** SMBIOS 机型仍为 MacBookPro16,2。这些演示值用于保护私人身份信息，不代表 Apple 服务已激活或具有真实设备身份。

包中保留启动器、启用的 ACPI 表、UEFI 驱动、kext bundle、配置文件、NVIDIA 回退工具及 OpenCanopy 所需图标、标签和位图字体，另附可选 Windows 显示标记模板、README、上游许可证声明、校验清单和 `.gitignore`。启用的 kext 保留原有插件 / 资源；VoodooI2C 的 Info.plist 包含下述本机控制器适配。

已移除真实三码、MAC / ROM、热点配置、旧蓝牙控制器状态、历史配置与 kext 备份、启动日志、截图、本地脚本和磁盘镜像。当前使用 **OpenCanopy 图形菜单**，包含 GoldenGate 主题图标、标签和位图字体，不附带壁纸、音频辅助资源和未使用的文本字体。Windows 磁盘设备路径已改为禁用模板，NVRAM 中保存的默认启动选择不随包分发。EFI 包不重新分发系统目录中的 NVIDIA 驱动、GPU bundle 和固件。

所有启用文件路径、ACPI 校验和及 OpenCanopy 必需资源均已检查。导出配置通过**与当前 OpenCore 完全相同构建中的 ocvalidate 1.0.9** 校验。OpenCore 核心二进制及启用的硬件 kext 保持原样，身份与磁盘路径脱敏仅作用于公开副本。静态校验不能替代实际启动测试。

### 使用前

1. 核对确切机型、BIOS、核心拓扑和硬件，备份可用 EFI，并保留回退 U 盘。
2. 在本机替换演示身份和 ROM；检查本机 DSDT 和 USB 映射，不假设它们适用于其他电脑。
3. 从 NVIDIA 上游项目取得驱动，遵循对应版本安装步骤。本包记录的是已装好驱动的状态。
4. 按设备型号确认 **macOS 硬盘的 EFI 分区** 后复制；不要套用固定 `diskXsY`，不要覆盖 Windows EFI。
5. 先验证启动、扬声器 / 耳机、无线、USB 和显示表现，再继续调整。更改 BIOS 核心设置或升级系统后需要重新验证。

### 上游与致谢

使用 [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)、[OcBinaryData](https://github.com/acidanthera/OcBinaryData)、[Lilu](https://github.com/acidanthera/Lilu)、[AppleALC](https://github.com/acidanthera/AppleALC)、[WhateverGreen](https://github.com/acidanthera/WhateverGreen)、[VirtualSMC](https://github.com/acidanthera/VirtualSMC)、[NVMeFix](https://github.com/acidanthera/NVMeFix)、[RestrictEvents](https://github.com/acidanthera/RestrictEvents)、[BrightnessKeys](https://github.com/acidanthera/BrightnessKeys)、[laobamac/itlwm](https://github.com/laobamac/itlwm)、[USBToolBox](https://github.com/USBToolBox/kext)、[VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)、[VoodooPS2](https://github.com/acidanthera/VoodooPS2) 和 [Realtek RTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X)。[nullmoth NVIDIA 驱动](https://github.com/nullmoth/nvidia-macos-driver)单独安装。

第三方组件保留作者署名和上游许可证，见 `LICENSES/` 和 `THIRD_PARTY_NOTICES.md`。组合包未对这些组件统一重新授权。
