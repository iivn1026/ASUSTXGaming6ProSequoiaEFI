# ASUS TX Gaming 6 Pro — macOS 26 EFI

[English](#english) · [简体中文](#简体中文)

> **WARNING / 警告：Read this README completely before installation. 安装前必须认真阅读完整 README。Back up a working EFI and keep a recovery USB. 必须保留可启动的 EFI 备份和恢复 U 盘。**

## English

### Scope

The `macOS26` branch contains a sanitized export of the working internal OpenCore EFI for **ASUS TX Gaming 6 Pro / FX608LM**, checked on **16 October 2026**, running **macOS Tahoe 26.7.1 (25G241)**. The `main` branch retains the Sequoia configuration.

This is an EFI package. NVIDIA system drivers, HiDPI framebuffer modifications, restored AppleHDA, KDKs, kernel collections and per-machine recovery backups are **not included**. Copying this EFI alone does not reproduce an already patched macOS installation. The working source boots; the public copy has different identity information and a disabled Windows template and has not been boot-tested separately.

### Hardware

| Component | Specification |
|---|---|
| Laptop | ASUS TX Gaming 6 Pro, FX608LM |
| BIOS baseline | FX608LM.315 |
| CPU | Intel Core Ultra 7 255HX, Arrow Lake-HX; 20 cores / 20 threads online |
| Memory | 32 GB |
| Dedicated GPU | NVIDIA GeForce RTX 5060 Laptop GPU, `10DE:2D59`, 8 GB |
| Integrated GPU | Intel `8086:7D67`; disabled in this configuration |
| Display | 16-inch internal panel, 2560 × 1600, 165 Hz |
| Wireless | Intel AX210; Wi-Fi `8086:2725`, Bluetooth USB `8087:0032` |
| Ethernet | Realtek RTL8111 family, `10EC:8168` |
| Audio | Realtek ALC256, `10EC:0256`, subsystem `1043:1064`; Intel SST `8086:7F50` |
| macOS SSD | SanDisk Ultra 3D NVMe, 500 GB, separate 512 MiB EFI partition |
| Windows SSD | Samsung MZVL81T0HELB-00BTW, 1 TB |
| SMBIOS | MacBookPro16,2; public identity values are randomized |
| Bootloader | OpenCore / OpenCanopy DBG-109-2026-09-30, schema 1.0.9 |
| Tested system | macOS Tahoe 26.7.1, build 25G241 |

### Device status

| Feature | Status |
|---|---|
| Internal SSD boot | Working without the installer USB |
| CPU | 20 active cores / threads; complete power-management testing remains pending |
| NVIDIA display | Working with separately installed 1401 / NullMoth system drivers; RTX 5060 Laptop and 8 GB reported |
| HiDPI | Working in the patched system; the current display reports 2560 × 1600 physical pixels and a 1280 × 800 logical desktop. Requires a separately patched NVRMFB; EFI arguments alone do not implement it |
| Native Wi-Fi | Working with the Tahoe AirportItlwm 2.4.0 alpha build |
| Bluetooth | Working; IntelBluetoothFirmware, IntelBTPatcher and BlueToolFixup retained |
| Touchpad | Working with the existing ASCE1200 I2C-HID controller adaptation and polling mode |
| Audio | AppleHDA restored separately with AppleALC 1.9.9 and `alcid=97`; built-in speaker outputs and the internal microphone are enumerated. Speaker, headphone and internal microphone operation was confirmed in the earlier working configuration; headset microphone and line-in recording are not verified |
| USB | Machine-specific UTBMap retained; not every port, orientation and transfer speed has been tested |
| Keyboard / external mouse | Working; all special keys are not verified |
| Battery | Charge / AC status reporting available; endurance is not verified |
| Ethernet | RTL8111 driver included; wired connection testing pending |
| Sleep / wake, HDMI audio, sensors, Apple services | Not fully verified |
| AirDrop / Continuity | Not claimed; native Wi-Fi and Bluetooth availability does not establish AWDL / AirDrop support |

### Tahoe changes

- Two AirportItlwm bundles are retained with mutually exclusive kernel ranges: `AirportItlwm.kext` for Darwin **24.2.0–24.99.99**, and `AirportItlwm-Tahoe.kext` for **25.0.0–25.99.99**. Both have the same bundle identifier; only the matching one may load. Do not remove the ranges or enable itlwm alongside them.
- The Tahoe binary is the prepared **2.4.0 alpha `bbe368e`** build from the laobamac fork. The Sequoia binary is retained for the existing OS-specific configuration. Neither entry covers future Darwin major versions automatically.
- Existing CPU spoofing, ACPI, Darwin 25 kernel patches, USB mapping, touchpad adaptation, Bluetooth initialization, security settings and graphics boot arguments are retained.
- `alcid=97` is retained. Tahoe audio requires **AppleHDA restoration in the OS**, in addition to AppleALC in EFI. The working installation was repaired using the **exact 25G241 KDK**, with original snapshots and collections retained before rebuilding. No AppleHDA or system-patching installer is distributed here.
- `nvfbhidpi=1`, `nvfbhidpiscaled=1` and the existing NVIDIA arguments are retained for the separately installed experimental framebuffer. They do not replace NVIDIA driver installation.
- `TXGamingFanBridge.kext` remains limited to Darwin 24 by the working config; its inclusion does not imply Tahoe fan-control support.
- Boot arguments are copied from the current configuration without adding or removing `-v`; this export currently has no standalone `-v` argument.

### Graphical boot menu

OpenCanopy uses **Acidanthera\\GoldenGate** icons with a **plain black background and no wallpaper**. The local setup displays **macOS first, Windows second**, defaults to macOS and waits **5 seconds**. Press **Space** to reveal recovery and auxiliary tools.

`InstanceIdentifier=TXGaming` is required for the supplied visibility markers. An empty identifier makes instance-specific markers ineffective and can expose the OpenCore volume and a duplicate Windows entry.

The public Windows entry is a **disabled template with a zero partition UUID**. Its private SSD path and saved NVRAM default selection are not distributed. To configure another installation:

1. Back up both EFI partitions. Obtain the actual Windows `bootmgfw.efi` UEFI device path from an OpenCore debug log, replace the template path and enable that entry.
2. Only after the custom Windows entry is correct, copy `Extras/Windows-visibility/EFI/` markers to the matching paths on the **Windows EFI partition**. These hide the duplicate automatic Windows entry only for the `TXGaming` instance; they do not replace the Windows loader.
3. Keep `InstanceIdentifier=TXGaming` and `HideAuxiliary=true`. Select macOS and press **Ctrl + Enter** once to save it as the default. Place the internal OpenCore entry first in BIOS boot priority.
4. For automatic Windows discovery instead, leave the template disabled and remove the instance-specific marker next to `EFI/Microsoft/Boot/bootmgfw.efi` if present. Automatic ordering follows firmware enumeration.

### Installation

1. Confirm the exact hardware, BIOS and CPU topology. This configuration contains machine-specific DSDT, APIC and USB mapping; it is not a universal laptop EFI.
2. Back up the **complete current EFI** and important files. Prepare and test a bootable recovery USB before replacing the internal EFI. A bootloader backup does not back up macOS system patches or user data.
3. Download the **macOS26 branch**, then verify `SHA256SUMS.txt` from the extracted root with `shasum -a 256 -c SHA256SUMS.txt`.
4. Generate a private SMBIOS identity and replace `SystemSerialNumber`, `MLB`, `SystemUUID` and `ROM` under `PlatformInfo → Generic`. Do not reuse the public demonstration values for Apple services.
5. Review `config.plist` in an editor compatible with OpenCore **1.0.9**. Keep the OS-specific Wi-Fi ranges, existing `alcid=97` and required boot arguments. Validate with the matching **ocvalidate 1.0.9**, not an older OCC validator.
6. Test the sanitized EFI from a spare USB first. Copy the supplied `EFI/BOOT` and `EFI/OC` to the intended EFI partition after preserving its old contents. **Do not format the disk or overwrite `EFI/Microsoft`.** Keep the original known-working USB separately.
7. Install OS-specific NVIDIA components using the upstream procedure if they are not already installed. HiDPI requires the separate [1401NVDriverHiDpiFix](https://github.com/iivn1026/1401NVDriverHiDpiFix) procedure and its own backup / rollback instructions.
8. For Tahoe audio, AppleALC in this package is insufficient on an OS without AppleHDA. Back up the system patch state, use resources matching the exact OS build, restore AppleHDA and rebuild compatible collections using the chosen patcher's documented procedure. Keep its Recovery rollback instructions offline. Do not copy kernel collections or private rollback packages from another machine.
9. Once macOS and required devices work from USB, install the tested EFI on the internal EFI partition and configure Windows / default boot as above.

### Rollback, including an unbootable system

- **EFI-only failure:** enter the firmware boot menu and boot the previously tested recovery USB's OpenCore. Select the installed macOS, mount the internal EFI, and restore its complete saved `EFI/BOOT` and `EFI/OC`. Preserve `EFI/Microsoft`. Reboot and select the internal OpenCore.
- **No internal macOS entry or black screen:** try the known-working USB first. Press Space for Recovery / auxiliary options. The NVIDIA recovery tool can remove NVIDIA components; it is not a general-purpose rollback and can remove graphics functionality.
- **System driver / root-patch failure:** changing EFI alone does not restore `/System/Library/Extensions`, kernel collections, GPU bundles or a selected APFS snapshot. Enter macOS Recovery from OpenCore, unlock / mount the installed system and data volumes, and follow the **same machine's matching patcher rollback instructions**. Restore the paired original snapshot and collections. Never substitute Sequoia caches for Tahoe caches.
- **Windows entry missing:** remove the instance-specific Windows visibility marker to restore automatic scanning, or correct the custom Windows device path. The original Windows bootloader must remain present.
- Do not reset NVRAM as a routine repair. It can change boot defaults and firmware entries without undoing OS patches. Do not delete old snapshots until the replacement system is verified.

### Known issues

1. **Intermittent startup screen flicker remains unresolved.** Display transitions can flash during boot.
2. The Wi-Fi build is alpha. WPA3-only networks, AWDL / AirDrop and full Continuity are not established as working.
3. An earlier Chrome / driver combination used software rendering. Current application acceleration needs separate testing; GPU detection and working HiDPI do not prove browser acceleration.
4. OS or NVIDIA updates may replace the patched framebuffer or restored AppleHDA and require repair. Preserve exact-build backups and recheck Wi-Fi kernel ranges after updates.
5. Long-term sleep / wake, all USB ports, stress tests and other laptop models remain unverified.

### Privacy and contents

The public copy has freshly randomized serial / MLB, UUID and locally administered ROM values. Real device identities, disk-specific Windows paths, NVRAM boot selections, Wi-Fi credentials, logs, obsolete configs, backups, screenshots and local recovery files are excluded. Required zero-filled Bluetooth initialization values remain intact. The live EFI is not changed during export.

Included: enabled EFI components and required bundle resources, active GoldenGate graphics / fonts, machine-specific ACPI and USB map, optional Windows visibility markers, license notices and checksums. Third-party licenses remain in `LICENSES/` and `THIRD_PARTY_NOTICES.md`. A successful static check does not guarantee booting on a different machine.

## 简体中文

### 适用范围

`macOS26` 分支为 **ASUS TX Gaming 6 Pro / FX608LM** 当前可用内置 EFI 的脱敏副本，核对日期为 **2026 年 10 月 16 日**，运行系统为 **macOS Tahoe 26.7.1（25G241）**。`main` 分支保留原 Sequoia 配置。

本仓库仅包含 EFI。**不包含** NVIDIA 系统驱动、HiDPI 帧缓冲修改、恢复后的 AppleHDA、KDK、内核集合及本机专用回退备份。仅复制 EFI 不等于完成系统驱动和根卷补丁安装。源配置可用；公开副本更换了身份信息，并禁用了 Windows 模板，未单独进行启动测试。

### 硬件

| 部件 | 规格 |
|---|---|
| 机型 | ASUS TX Gaming 6 Pro，FX608LM |
| BIOS 基线 | FX608LM.315 |
| CPU | Intel Core Ultra 7 255HX，Arrow Lake-HX，20 核 / 20 线程在线 |
| 内存 | 32 GB |
| 独显 | NVIDIA GeForce RTX 5060 Laptop GPU，`10DE:2D59`，8 GB |
| 核显 | Intel `8086:7D67`，当前配置禁用 |
| 内屏 | 16 英寸，2560 × 1600，165 Hz |
| 无线 | Intel AX210；Wi-Fi `8086:2725`，蓝牙 USB `8087:0032` |
| 有线网卡 | Realtek RTL8111 系列，`10EC:8168` |
| 声卡 | Realtek ALC256，`10EC:0256`，子系统 `1043:1064`；Intel SST `8086:7F50` |
| macOS 硬盘 | SanDisk Ultra 3D NVMe 500 GB，独立 512 MiB EFI 分区 |
| Windows 硬盘 | Samsung MZVL81T0HELB-00BTW 1 TB |
| SMBIOS | MacBookPro16,2，公开身份值已随机替换 |
| 引导 | OpenCore / OpenCanopy DBG-109-2026-09-30，配置格式 1.0.9 |
| 已测试系统 | macOS Tahoe 26.7.1，25G241 |

### 设备状态

| 功能 | 状态 |
|---|---|
| 内置盘启动 | 可用，无需安装 U 盘 |
| CPU | 20 核 / 20 线程在线；完整电源管理测试尚未完成 |
| NVIDIA 显示 | 配合单独安装的 1401 / NullMoth 系统驱动可用，识别 RTX 5060 Laptop 和 8 GB 显存 |
| HiDPI | 已修改的系统中可用；当前物理像素为 2560 × 1600，逻辑桌面为 1280 × 800。需要单独修改 NVRMFB，EFI 参数不能独立实现 |
| 原生 Wi-Fi | Tahoe 版 AirportItlwm 2.4.0 alpha 可用 |
| 蓝牙 | 可用，保留 IntelBluetoothFirmware、IntelBTPatcher 和 BlueToolFixup |
| 触控板 | 可用，保留 ASCE1200 I2C-HID 控制器适配和轮询模式 |
| 声卡 | 系统已单独恢复 AppleHDA，配合 AppleALC 1.9.9 和 `alcid=97`；内置扬声器输出与内置麦克风已枚举。此前可用配置已确认扬声器、耳机和内置麦克风功能；耳麦麦克风及线路输入录音未验证 |
| USB | 保留本机 UTBMap；未逐一完成所有接口、正反插及速度测试 |
| 键盘 / 外接鼠标 | 可用；部分特殊键未验证 |
| 电池 | 可读取电量和充电状态；续航未验证 |
| 有线网络 | 包含 RTL8111 驱动，插线联网未验证 |
| 睡眠唤醒、HDMI 音频、传感器、Apple 服务 | 未完整验证 |
| AirDrop / 连续互通 | 不宣称可用；Wi-Fi 与蓝牙可用不代表 AWDL / AirDrop 可用 |

### Tahoe 配置变化

- 保留两个 AirportItlwm，通过互斥内核范围选择：`AirportItlwm.kext` 适用于 Darwin **24.2.0–24.99.99**，`AirportItlwm-Tahoe.kext` 适用于 **25.0.0–25.99.99**。两者 bundle 标识相同，只能加载对应系统的一份。不要删除版本范围，也不要同时启用 itlwm。
- Tahoe 使用已准备的 laobamac 分支 **2.4.0 alpha `bbe368e`** 构建。保留 Sequoia 驱动用于已有的系统版本配置，不自动覆盖未来 Darwin 大版本。
- 保留现有 CPU 仿冒、ACPI、Darwin 25 内核补丁、USB 映射、触控板适配、蓝牙初始化、安全设置及显卡参数。
- 保留 `alcid=97`。Tahoe 系统缺少 AppleHDA 时，需要**额外在系统中恢复 AppleHDA**。当前安装使用准确匹配 **25G241 的 KDK**，保存原快照和内核集合后完成重建。本仓库不分发 AppleHDA 或系统补丁安装器。
- 保留 `nvfbhidpi=1`、`nvfbhidpiscaled=1` 及现有 NVIDIA 参数，配合另行安装的实验帧缓冲使用；这些参数不能替代显卡驱动安装。
- `TXGamingFanBridge.kext` 仍按源配置限制在 Darwin 24；包含该文件不代表支持 Tahoe 风扇控制。
- 启动参数按当前配置保留，不额外增删 `-v`；本次源配置没有独立的 `-v` 参数。

### 图形启动菜单

OpenCanopy 使用 **Acidanthera\\GoldenGate** 图标，**纯黑背景，无壁纸**。本地菜单为 **macOS 第一、Windows 第二**，默认 macOS，等待 **5 秒**。按**空格**显示恢复与辅助工具。

显示规则依赖 `InstanceIdentifier=TXGaming`。该字段为空时，针对实例的隐藏标记失效，可能多出 OpenCore 分区及重复 Windows 入口。

公开 Windows 项为**禁用模板，分区 UUID 全零**，不包含真实 SSD 路径或本机 NVRAM 默认选择。部署方法：

1. 备份两块盘的 EFI。从 OC 调试日志获取目标 Windows `bootmgfw.efi` 的完整 UEFI 路径，替换模板并启用。
2. 确认自定义 Windows 项正确后，再把 `Extras/Windows-visibility/EFI/` 中的标记复制到 **Windows EFI 分区**对应目录。它们仅对 `TXGaming` 实例隐藏重复自动入口，不替换 Windows 启动器。
3. 保留 `InstanceIdentifier=TXGaming`、`HideAuxiliary=true`。在 OC 中选中 macOS，按一次 **Ctrl + Enter** 保存默认；在 BIOS 中把内置 OpenCore 放在启动首位。
4. 若使用自动扫描 Windows，则保持模板禁用，并移除 `EFI/Microsoft/Boot/bootmgfw.efi` 旁已有的实例隐藏标记。自动排列顺序由固件枚举决定。

### 安装方法

1. 核对机型、BIOS 和 CPU 拓扑。本 EFI 含本机专用 DSDT、APIC 和 USB 映射，不是通用笔记本 EFI。
2. 备份**完整现有 EFI**及重要文件。修改内置盘前，准备并实际测试可启动的恢复 U 盘。EFI 备份不包含系统补丁和个人文件。
3. 下载 **macOS26 分支**，在解压根目录运行 `shasum -a 256 -c SHA256SUMS.txt` 校验文件。
4. 生成私人 SMBIOS 身份，替换 `PlatformInfo → Generic` 中的 `SystemSerialNumber`、`MLB`、`SystemUUID`、`ROM`。不要共用公开演示值登录 Apple 服务。
5. 用兼容 OpenCore **1.0.9** 的编辑器检查配置。保留 Wi-Fi 内核范围、`alcid=97` 及必要启动参数，使用匹配的 **ocvalidate 1.0.9** 校验，不能使用旧版 OCC 校验器。
6. 先用另一只 U 盘测试脱敏 EFI。备份目标 EFI 后再复制 `EFI/BOOT`、`EFI/OC`。**不要格式化磁盘，不要覆盖 `EFI/Microsoft`。** 原可用 U 盘另行保留。
7. 若系统尚未安装 NVIDIA 组件，按上游方法安装对应系统的驱动。HiDPI 另见 [1401NVDriverHiDpiFix](https://github.com/iivn1026/1401NVDriverHiDpiFix)，遵守该项目自己的备份和回退方法。
8. Tahoe 缺少 AppleHDA 时，仅有本包 AppleALC 无法恢复声音。先备份系统补丁状态，使用匹配当前系统构建的资源，按所选补丁工具的文档恢复 AppleHDA 和重建兼容内核集合，离线保存 Recovery 回退方法。不要复制另一台机器的内核集合或私人回退包。
9. U 盘启动且所需设备验证完成后，再部署到内置 EFI，并按上文配置 Windows 和默认启动。

### 回退方法，包括无法进入系统

- **仅 EFI 导致启动失败：** 从 BIOS 启动菜单选择原可用 U 盘的 OpenCore，启动内置 macOS，挂载内置 EFI，恢复原先完整备份的 `EFI/BOOT`、`EFI/OC`，保留 `EFI/Microsoft`，重启后选择内置 OpenCore。
- **看不到 macOS 或黑屏：** 优先使用已验证的备用 U 盘。按空格查看 Recovery 和辅助入口。NVIDIA 回退工具用于移除 NVIDIA 组件，可能同时失去显卡功能，不能当作通用回退工具。
- **系统驱动 / 根卷补丁导致启动失败：** 仅换 EFI 不会恢复系统扩展、内核集合、GPU bundle 或已选 APFS 快照。通过 OpenCore 进入恢复环境，解锁并挂载系统卷及数据卷，按**本机对应补丁工具的回退说明**恢复配套原快照和集合。不能把 Sequoia 缓存替换到 Tahoe。
- **Windows 入口消失：** 移除 Windows 的实例隐藏标记以恢复自动扫描，或修正自定义 Windows 路径。必须保留原 Windows 启动器。
- 不把 Reset NVRAM 当作常规修复：它会改变默认启动和固件入口，不能撤销系统补丁。验证新系统前不要删除旧快照。

### 已知问题

1. **开机偶发闪屏仍未解决。** 启动显示切换阶段可能闪烁。
2. Wi-Fi 为 alpha 构建，不宣称纯 WPA3、AWDL / AirDrop 或完整连续互通可用。
3. 早期 Chrome 与驱动组合使用软件渲染。当前应用加速需要独立验证，识别显卡及 HiDPI 生效不能证明浏览器硬件加速。
4. 系统或 NVIDIA 更新可能替换修改后的帧缓冲和 AppleHDA。保留准确版本的备份，更新后检查 Wi-Fi 内核范围。
5. 长期睡眠唤醒、全部 USB 接口、压力测试和其他机型兼容性未完整验证。

### 脱敏与文件范围

公开副本重新随机生成序列号、MLB、UUID 和本地管理 ROM。真实身份、磁盘专用 Windows 路径、默认启动 NVRAM、Wi-Fi 密码、日志、旧配置、备份、截图和本机回退文件均排除。保留必要的全零蓝牙初始化值。导出过程不修改实际 EFI。

仅包含启用的 EFI 组件及必要 bundle 资源、当前 GoldenGate 图标和字体、本机 ACPI / USB 映射、可选 Windows 显示标记、许可证和校验清单。第三方条款见 `LICENSES/` 和 `THIRD_PARTY_NOTICES.md`。静态校验通过不代表能在另一台机器上启动。
