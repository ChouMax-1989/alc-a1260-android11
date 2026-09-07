# ALC 安卓遥控器 A1260：Android 11 固件与刷机说明

本包面向 ALC Android remote，型号 A1260，MT6580，Android 11。

这是一套给 A1260 使用的单槽 eMMC 系统镜像。镜像保留了 A1260 所需的原厂 vendor 能力，实机已经验收过桌面、Wi-Fi、蓝牙、扬声器、红外和文件下载。

本说明只写 A1260 已验证过的范围。刷写会改动设备闪存。每一步都要先核对文件大小和 SHA-256，再继续。

## 发布文件

| 文件 | 大小 | SHA-256 | 用途 |
|---|---:|---|---|
| `a1260-android11-system-current.img` | 1,610,612,736 字节 | `984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385` | 当前 A1260 Android 11 system 精确回读 |
| `a1260-boot-android11-usap-v1.img` | 16,777,216 字节 | `52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878` | 当前 A1260 Android 11 boot 精确回读 |
| `Square Home 2.1.14` APK 与隐私清理数据 | — | — | 当前实机的两套桌面布局 |
| `iControl 5.2.5` APK 与干净数据 | — | — | 退出登录、无遥控器 |

boot 和 system 文件都是当前 A1260 分区的精确回读匹配。另一个 v8 镜像只是已被 superseded 的构建证据，不是本包的用户刷写文件。

公开包包含经隐私清理的 Square Home 2.1.14 APK、当前实机的两套桌面布局和显示设置，也包含 iControl 5.2.5 APK 与干净数据。iControl 打开时是退出登录、空白无遥控器的状态；原机检测到的 5 个遥控器、全部按钮、场景、空调状态、内置或学习红外记录、账号和 token 都已删除。Square Home 布局结构和显示设置保留，只清掉设备环境标记、首次运行时间、缓存和日志。

不要把 Wi-Fi 密码、蓝牙配对、设备序列号、个人红外学习码或 profile、旧账号和 token、个人应用数据、其他第三方 APK、测试主机的 ADB 公钥、设备识别信息、日志或测试记录放进公开包。包内的 Square Home 清理数据和 iControl 干净数据是发布内容的一部分。

## 两层功能

第一层在 boot 和 system 镜像里。它提供 A1260 实机所需的 Wi-Fi、蓝牙、扬声器、vendor 红外节点和 FUSE 外部存储支持。红外由 A1260 的 vendor 节点和 iControl 路径工作，不依赖标准 Consumer IR API。

第二层是第一次进入桌面后执行一次的 post-install 配置。它会停用无摄像头用途的图库组件，保留 MediaProvider、文件组件和下载组件；恢复 Basic Dreams 和 PhotoTable 屏保，打开睡眠和底座触发；安装 Square Home 与 iControl，并写入已清理的桌面布局和 iControl 空白状态。运行发布包内的一次性 post-install 脚本；没有这个脚本时，不要自行拼接命令或增加清理、优化操作。

非个人设置和清理 profile restore 已做静态校验，但这套清理 profile restore 没有在源设备上实际执行。首次运行后必须按本说明人工检查桌面与 iControl 初始状态。

Wi-Fi 密码、蓝牙配对、红外学习码和旧账号数据都不属于这层配置。刷完后请在设备上重新输入 Wi-Fi 密码、重新配对蓝牙；打开 iControl 后先登录，再创建自己的遥控器并重新学习红外。Square Home 使用的是包内经过隐私清理的当前实机布局。

## 开始前

准备一台 Windows 电脑、质量正常的数据线和电量充足的 A1260。另行从可信来源准备 MediaTek PreLoader USB VCOM 驱动、Android platform-tools 和 SP Flash Tool；本机验证使用的是 SP Flash Tool v5.2032。公开包不重复分发这些第三方工具。关闭其他刷机工具。

先把设备中的重要内容自行保存到别处。刷写 system 或 boot 可能导致设备无法启动。本文不提供用户内容备份，也不会清空或迁移用户内容。

本说明的 Write Memory 步骤只写 boot 或 system 区间，正常情况下会保留设备的 `/data`，所以已安装的应用和设置可以继续使用。包内也带有经过隐私清理的 Square Home 数据和干净的 iControl 数据。若你主动执行了清空数据的操作，Square Home 和 iControl 必须按系统安装流程重新安装；只能使用包内副本或其他合法来源，不要恢复旧的账号、token、遥控器 profile、学习码或个人应用数据。

在电脑上打开 PowerShell，进入存放镜像的文件夹，分别执行：

```powershell
Get-FileHash .\firmware\a1260-android11-system-current.img -Algorithm SHA256
Get-FileHash .\firmware\a1260-boot-android11-usap-v1.img -Algorithm SHA256
```

输出必须与上表完全一致。大小也必须一致。任何一个不一致都停止，不要打开 SP Flash Tool 写入。

## 已验证的 SP Flash Tool 方法

使用 SP Flash Tool 的 **Advanced Mode > Write Memory**。这条路径绕过了 A1260 上已出现的 PMT 比较错误，只写一个明确的地址区间。

### 写入 boot

这是会改闪存的步骤。

1. 启动 `flash_tool.exe`。
2. 打开 `Window` 菜单，选择 `Write Memory`。如果窗口未显示，先进入 `Advanced Mode`。
3. `Region` 选择 `EMMC_USER`。
4. `Begin Address (HEX)` 填 `0x01D20000`。
5. `File Path` 选择 `firmware` 文件夹里的 `a1260-boot-android11-usap-v1.img`。
6. 点击 `Write Memory`。
7. 设备完全关机后直接插 USB。不要按音量键，不要拆机短接。等待 SP Flash Tool 显示绿色完成标志。
8. 写入完成后不要开机。打开 `Readback`，使用相同的 `Region`、起始地址 `0x01D20000` 和长度 `0x01000000`，保存回读文件。
9. 回读完成后，在电脑上计算回读文件 SHA-256。必须等于 `52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878`，再进行下一步。

### 写入 Android 11 system

这是会改闪存的步骤。建议先完成 boot 的写入和回读，再写 system。

1. 仍在 `Write Memory` 窗口中，把 `Region` 保持为 `EMMC_USER`。
2. `Begin Address (HEX)` 填 `0x0A800000`。
3. `File Path` 选择 `firmware` 文件夹里的 `a1260-android11-system-current.img`。
4. 点击 `Write Memory`，设备保持完全关机并直接插 USB。
5. 等待绿色完成标志。不要在进度条未完成时拔线或按电源键。
6. 打开 `Readback`，使用相同的 `Region`、起始地址 `0x0A800000` 和长度 `0x60000000` 回读整段。
7. 回读文件 SHA-256 必须等于 `984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385`。不匹配就停止，不要启动 Android。

system 和 boot 都回读匹配后，拔掉 USB，按电源键开机。公开包不提供 recovery 镜像，也没有 Recovery/ADB 刷写步骤。不要从网上下载 Recovery ZIP，也不要使用 fastboot 或第三方 recovery 镜像。

## 第一次开机

第一次开机可能比平时久。看到开机画面后保持供电，不要反复强制重启。

进入桌面并打开 USB 调试后，把电脑上的 `platform-tools` 文件夹放在发布包根目录，然后运行：

```powershell
pwsh -File .\setup\post-install.ps1 -StaticValidate
pwsh -File .\setup\post-install.ps1
```

脚本会先检查文件和设备，安装当前桌面布局与干净的 iControl，再重启一次。完成后检查：

- `设置 > 关于平板电脑` 中确认系统为 Android 11；
- 桌面可以正常打开，系统不持续重启；
- Wi-Fi 能连接并访问网络；
- 蓝牙可以打开并实际连接设备；
- 扬声器能正常出声；
- iControl 首次打开时显示退出登录且没有遥控器；登录后创建自己的遥控器，再学习红外并确认可以发射；
- Square Home 可以启动并显示包内经过隐私清理的当前实机布局；
- 文件应用能看到 `Download`，浏览器下载一个小文件并能打开；
- 屏保保持启用，睡眠和底座触发均可用。

Wi-Fi 和蓝牙功能来自 boot/system 层，但凭据和配对记录不在公开包内。配置完成后要由使用者重新输入 Wi-Fi 密码并重新配对蓝牙。红外功能来自 boot/system 层和 vendor 节点；iControl APK 虽随包提供，但账号、token、遥控器、按钮、场景、空调状态、内置或学习红外行均已清空。每位使用者都要登录自己的账号、创建自己的遥控器并重新学习红外。

红外使用的是 A1260 的 vendor 节点和 iControl 路径。Android 标准 Consumer IR API 报告不支持属于已知现象，不代表 A1260 红外失效。

不要停用 Android 11 的 MediaProvider、DocumentsUI 或下载组件。图库应用在这个无摄像头的遥控器上默认停用，这是预期行为。

## 遇到问题时

### SP Flash Tool 显示 PMT changed

立即停止。A1260 的完整 scatter 路径已实测会在 DA 100% 后报 `PMT changed`，且没有写入分区。

关闭当前操作，重新确认只使用 `Advanced Mode > Write Memory`，只填写本说明给出的 `Region`、起始地址、镜像和长度。不要改分区表，不要选 `Firmware Upgrade`，不要选 `Format All + Download`。

### Recovery 菜单能进，但没有 ADB

这是预期限制。A1260 原厂 recovery 的 ADB 行为不等于 Android 11 的公开刷机接口；公开包不提供自制 Recovery ZIP，也不建议在 recovery 中选择 `Apply update` 安装未知 ZIP。

如果 boot 和 system 的回读已经通过，退出 recovery，按电源键正常启动 Android。不要因为 recovery 没有 ADB 就继续写其他分区。

### Android 启动后电脑看不到 ADB

先等待系统进入桌面，再执行 `adb devices`。检查 USB 线、驱动和设备管理器中的 MediaTek/Android 设备，然后拔插一次 USB。设备未进入桌面、未完成首次启动或没有弹出 USB 调试授权时，不要执行写入命令。

如果始终没有 ADB，不要连续换刷多个镜像。回到“回滚”一节，只使用你自己在刷机前制作并核对过 SHA-256 的 system/boot 备份。

### 写入后黑屏、卡在开机画面或反复重启

先长按电源键约 10 秒尝试一次完整关机，再重新开机。若仍不能进入桌面，停止继续试错。确认回读文件与发布 SHA-256 一致；若回读不一致，不能把问题归因于 Android 本身。

## 回滚

公开包不提供原系统备份。每位用户都应在刷机前，从自己的 A1260 制作 system 和 boot 的完整备份，并在电脑上保存文件大小与 SHA-256。没有自己核对过的备份时，不要进行回滚，也不要从第三方下载 system 或 boot。

回滚使用同一条 `Write Memory` 路径。自己的 system 备份使用 `Region=EMMC_USER`、起始地址 `0x0A800000`、长度 `0x60000000`；自己的 boot 备份使用 `Region=EMMC_USER`、起始地址 `0x01D20000`、长度 `0x01000000`。每次只选择一个文件，写完后用 `Readback` 回读同一段，并确认回读 SHA-256 与自己的备份完全一致，才可以开机。

回滚也会改闪存；回读不匹配时保持关机，不要继续尝试。

## 已知限制

- 这是 A1260 单槽 eMMC 设备，没有 A/B 槽位切换，也没有已验证的 OTA 回退流程。
- 本说明只允许按指定地址写 boot、system，其他分区不要勾选或填写。
- 不提供可公开发布的 recovery 镜像，也没有已验证的 Recovery/ADB 或 OTA 回退流程。
- Android 11 的最终硬件验收使用了当前设备的 boot/system 分区；本包的 system 文件是该分区的精确回读文件，boot 文件也是当前设备的精确回读匹配。
- post-install 的清理 profile restore 通过静态校验，但没有在源设备上实际执行；首次运行后必须人工检查 Square Home 两套布局和 iControl 的退出登录、空白状态。
- 红外依赖 A1260 vendor 节点和 iControl；标准 Android Consumer IR API 显示不支持是已知限制。
- iControl 和 Square Home 随包提供的是隐私清理后的初始状态；iControl 没有登录状态和遥控器，Square Home 保留当前实机布局和显示设置。账号、token、遥控器、按钮、场景、空调状态、学习码、蓝牙配对和 Wi-Fi 凭据必须由使用者重新设置。
- 原厂语音助手、几何天气和图库不属于保留范围。图库停用不会停用文件、媒体提供程序或下载功能。

只要任一步的文件大小、SHA-256、SP Flash Tool 进度或 Readback 结果不符合本说明，就停止刷写并保留设备当前状态。
