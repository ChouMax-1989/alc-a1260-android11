# ALC 安卓遥控器 A1260：Android 11 v1.1.0 固件与刷机说明

本包面向 ALC/AIC Android remote，型号 A1260，MediaTek MT6580，Android 11，ARM32 Binder32。

[下载 v1.1.0 完整发布包](https://github.com/ChouMax-1989/alc-a1260-android11/releases/tag/v1.1.0)

v1.1.0 是完整刷机包：里面仍有 boot 和 system 镜像，也带本次公开的大字时钟 0.1.0 和屏保修正。boot/system 沿用此前已在 A1260 上验证过的镜像副本；这次没有声称重新编译，也没有声称本轮重新刷写或从空白设备完成整包验收。想保留已有桌面、遥控器和账号的设备，可以只运行更新脚本，不需要重刷镜像。

本固件不包含小智 AI，也不会改动设备上自行安装的小智。

刷写会改动设备闪存。每一步都要先核对文件大小和 SHA-256，再继续。下载入口只使用 v1.1.0；旧 v1.0.0 和 v1.0.1 在新包下载验证后撤下，本文不再推荐旧版本。

## 发布文件

| 文件 | 大小 | SHA-256 | 用途 |
| --- | ---: | --- | --- |
| firmware/a1260-android11-system-current.img | 1,610,612,736 字节 | 984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385 | A1260 Android 11 system 精确回读副本 |
| firmware/a1260-boot-android11-usap-v1.img | 16,777,216 字节 | 52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878 | A1260 Android 11 boot 精确回读副本 |
| apps/A1260-LegacyClock-0.1.0.apk | 35,712 字节 | 26d154e955b04784946973a472564abb4bfe94dfd563ed0bee427dd9381628f7 | 大字时钟显示适配 |
| profile/apks/com.ss.squarehome2/base.apk | 以 SHA256SUMS.txt 为准 | dff9dcb2ff7214cdb8c22035454d8930d4b6b478555f28fccfe2b2cd3b6d5f4e | Square Home 2.1.14 |
| profile/apks/com.tiqiaa.icontrol/base.apk | 以 SHA256SUMS.txt 为准 | 73ae6f252a2cff09e9d6aa718383b41802f508939923e618818b3bbb351e60de | iControl 5.2.5 |

boot 和 system 是此前当前 A1260 分区的精确回读匹配，大小和哈希沿用已验证记录。profile 里的 Square Home 2.1.14 保留当前实机的两套桌面布局和显示设置；iControl 5.2.5 是退出登录、没有遥控器的干净状态。iControl 原先的 5 个遥控器、193 个按键、26 条红外记录、6 个场景、空调状态、账号和 token 已清除。

旧固件屏保的完整原厂 APK、DEX、分析文件和缓存不在公开包内。公开包里的大字时钟是独立的显示适配；它不等于完整原厂屏保恢复。

## 这次更新

- v1.1.0 仍提供完整 boot/system 刷机包，沿用已验证镜像，不把本次描述成重新编译的系统。
- 首次配置脚本会安装 apps/ 下的大字时钟 0.1.0，并应用当前屏保修正。
- 新屏保使用组件 io.github.choumax.a1260clock/io.github.choumax.a1260clock.ClockDreamService。充电时设备闲置约 30 秒后进入屏保，显示白字黑底的大时间、完整日期和星期。显示效果及基座自动进入已由用户在当前设备确认。
- 如果设备安装了 VolumeMan 音量工具，且在“更多 → 输出源”打开了输出源，本次修正会关闭这个开关。VolumeMan 每约 0.5 秒更新音频，会不断重置系统闲置计时；关闭输出源可以让 30 秒屏保计时正常累积。工具本身不卸载，音量增强设置保留；若需要关闭原本开启的输出源，脚本会先暂停该工具以避免旧缓存写回，之后可重新打开音量工具使用增强。没有 VolumeMan 时自动跳过。

## 选择哪条配置路径

首次刷入 Android 11 后运行首次配置；已经使用这套 Android 11 的设备直接选择旧机更新，无需先刷 boot/system。两条路径都需要 Android 11 已进入桌面、USB 调试可用，并把 Android platform-tools 文件夹放在发布包根目录，使发布包根目录下能找到 platform-tools/adb.exe。

### 新机或明确重装：2-首次开机配置.cmd

这条是完整的一次性首次配置路径，只适用于新刷好的设备，或你已经明确要重装并接受清空两个应用数据的设备。它会：

1. 安装和设置 A1260 的红外、FUSE、非个人系统开关和屏保触发。
2. 安装 apps/ 下的大字时钟 0.1.0。
3. 安装 Square Home 2.1.14 与 iControl 5.2.5，并恢复公开包里的干净 profile。
4. 清除这两个应用现有的应用数据，把 Square Home 恢复到公开的两套桌面布局，并让 iControl 回到退出登录、无遥控器状态。
5. 应用大字时钟、30 秒屏保和 VolumeMan 输出源修正，然后按脚本提示重启并检查。

不要在已有个人桌面、iControl 遥控器或账号的设备上随意运行这条路径；它会清掉 Square Home 和 iControl 的现有数据。公开 profile 的静态、文件集合、数据库关系和哈希检查已完成，但清空并恢复 profile 没有在源设备上执行，以免破坏源机数据。

完成后请由使用者自己重新输入 Wi-Fi 密码、重新配对蓝牙、登录 iControl 并创建或学习自己的遥控器。

### 保留现有使用状态：3-更新现有设备.cmd

这条路径给已经在用的 A1260。它只安装或升级 apps/ 下的大字时钟 0.1.0，并应用当前屏保、30 秒闲置和 VolumeMan 输出源修正：

- 不重刷 boot/system；
- 不恢复 clean profile；
- 不清除 Square Home 桌面；
- 不清除 iControl 的遥控器、账号或登录数据；
- 不卸载 VolumeMan，也不关闭音量增强；
- 设备没有 VolumeMan 时跳过该项。

如果设备上已有自己的桌面、遥控器和账号，应使用这条路径。旧机更新脚本已在当前设备执行通过；更新前后6份桌面、遥控器数据库及独立APP设置/模板文件的哈希保持一致。这不等于从空白设备完成了整包刷写验收。

## 工具下载

- [Android platform-tools 官方页面](https://developer.android.com/tools/releases/platform-tools)
- [Windows platform-tools 官方直链](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)
- [MediaTek PreLoader USB VCOM 驱动：Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=MediaTek%20PreLoader%20USB%20VCOM)
- [SP Flash Tool：MediaTek 官方工具门户](https://online.mediatek.com/English/Tool)

本机实际使用的是 SP Flash Tool v5.2032。其他版本先确认有 Advanced Mode > Write Memory，不要加载陌生的 scatter、preloader 或 DA。公开包不重复分发这些第三方工具。

## 开始前

准备一台 Windows 电脑、质量正常的数据线和电量充足的 A1260。另行从可信来源准备 MediaTek PreLoader USB VCOM 驱动、Android platform-tools 和 SP Flash Tool。关闭其他刷机工具。

先把设备中的重要内容自行保存到别处。刷写 system 或 boot 可能导致设备无法启动。本文不提供用户内容备份，也不会清空或迁移用户内容。

Write Memory 只写下面明确的 boot 或 system 区间，正常情况下会保留设备的 /data；但 2-首次开机配置.cmd 会清除 Square Home 和 iControl 两个应用的数据，因此已有设备要改用 3-更新现有设备.cmd。无论哪条配置路径，Wi-Fi 密码、蓝牙配对、账号、token、遥控器和学习码都不在公开包里。

在电脑上打开 PowerShell，进入存放镜像的文件夹，分别执行：

    Get-FileHash .\firmware\a1260-android11-system-current.img -Algorithm SHA256
    Get-FileHash .\firmware\a1260-boot-android11-usap-v1.img -Algorithm SHA256

这只是下载完整性检查，不会改设备。正常下载且 ZIP 能完整解压时可以跳过；网络不稳或文件经过转存时建议检查。任何一个结果不一致都不要刷。

## 已验证的 SP Flash Tool 方法

使用 SP Flash Tool 的 Advanced Mode > Write Memory。这条路径绕过了 A1260 上已出现的 PMT 比较错误，只写一个明确的地址区间。以下 boot/system 的区域、起始地址、长度、写后 Readback 和 SHA-256 门槛沿用已验证方法，不要改成其他刷写模式。

### 写入 boot

这是会改闪存的步骤。

1. 启动 flash_tool.exe。
2. 打开 Window 菜单，选择 Write Memory。如果窗口未显示，先进入 Advanced Mode。
3. Region 选择 EMMC_USER。
4. Begin Address (HEX) 填 0x01D20000。
5. File Path 选择 firmware 文件夹里的 a1260-boot-android11-usap-v1.img。
6. 点击 Write Memory。
7. 设备完全关机后直接插 USB。不要按音量键，不要拆机短接。等待 SP Flash Tool 显示绿色完成标志。
8. 写入完成后不要开机。打开 Readback，使用相同的 Region、起始地址 0x01D20000 和长度 0x01000000，保存回读文件。
9. 回读完成后，在电脑上计算回读文件 SHA-256。必须等于 52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878，再进行下一步。

### 写入 Android 11 system

这是会改闪存的步骤。建议先完成 boot 的写入和回读，再写 system。

1. 仍在 Write Memory 窗口中，把 Region 保持为 EMMC_USER。
2. Begin Address (HEX) 填 0x0A800000。
3. File Path 选择 firmware 文件夹里的 a1260-android11-system-current.img。
4. 点击 Write Memory，设备保持完全关机并直接插 USB。
5. 等待绿色完成标志。不要在进度条未完成时拔线或按电源键。
6. 打开 Readback，使用相同的 Region、起始地址 0x0A800000 和长度 0x60000000 回读整段。
7. 回读文件 SHA-256 必须等于 984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385。不匹配就停止，不要启动 Android。

system 和 boot 都回读匹配后，拔掉 USB，按电源键开机。公开包不提供 recovery 镜像，也没有 Recovery/ADB 刷写步骤。不要从网上下载 Recovery ZIP，也不要使用 fastboot 或第三方 recovery 镜像。

## 第一次开机：新机或重装

第一次开机可能比平时久。看到开机画面后保持供电，不要反复强制重启。

进入桌面并打开 USB 调试后，确认 platform-tools/adb.exe 在发布包根目录下，双击 2-首次开机配置.cmd。脚本会自行检查输入，不用先运行静态检查。按脚本提示完成后等待设备重启。

这条路径会安装大字时钟 APK，并把 Square Home、iControl 恢复为公开的干净 profile。它适合新设备或明确重装；已有个人桌面、遥控器、账号时请改用 3-更新现有设备.cmd。

完成后检查：

- 设置 > 关于平板电脑中确认系统为 Android 11；
- 桌面可以正常打开，系统不持续重启；
- Wi-Fi 能连接并访问网络；
- 蓝牙可以打开并实际连接设备；
- 扬声器能正常出声；
- iControl 首次打开时退出登录且没有遥控器；
- Square Home 启动后显示包内经过隐私清理的两套布局；
- 把设备放在通电基座上，保持不触碰，约 30 秒后应进入白字黑底的大字时间、完整日期和星期屏保；
- 文件应用能看到 Download，浏览器下载一个小文件并能打开；
- 屏保保持启用，睡眠和底座触发可用。

Wi-Fi 和蓝牙功能来自 boot/system 层，但凭据和配对记录不在公开包内。红外功能来自 boot/system 层和 vendor 节点；iControl 的账号、token、遥控器、按钮、场景、空调状态、内置或学习红外行均已清空。每位使用者都要登录自己的账号、创建自己的遥控器并重新学习红外。

## 更新已有设备：不重刷、不清个人状态

设备已经可以正常进入 Android 11 桌面，且你要保留当前 Square Home 桌面、iControl 遥控器和账号时：

1. 先确保设备打开 USB 调试并显示在 adb devices 中。
2. 把 platform-tools 文件夹放在发布包根目录。
3. 双击 3-更新现有设备.cmd。
4. 按脚本提示等待 APK 更新和屏保修正完成。
5. 回到桌面确认原布局、遥控器和账号仍在，再检查大字时钟版本和 30 秒屏保。

这条路径不写 boot/system，也不执行 clean profile restore。不要为了“更新”而运行 2-首次开机配置.cmd。

## 大字时钟、屏保和 VolumeMan

大字时钟 APK 的包名是 io.github.choumax.a1260clock，屏保组件是 io.github.choumax.a1260clock/io.github.choumax.a1260clock.ClockDreamService。它是独立 Android 11 DreamService，只负责显示：白字、黑底、大时间、完整日期和星期。原 DeskClock 屏保未替换或卸载，可以作为回退组件。

当前修正把充电时的屏保闲置目标设为约 30 秒，并打开睡眠、基座触发和屏保组件。A1260 的基座自动进入和显示效果已在当前设备确认。屏保字体取自本机旧固件，只作为显示适配的一部分；它不是本项目原创字体，本文和仓库不对它声明 MIT 许可。

VolumeMan 的问题只在它存在且“更多 → 输出源”开启时出现：工具约每 0.5 秒更新一次音频，系统会把设备反复当成刚有活动，屏保闲置时间无法累积。v1.1.0 关闭的只是 output_source_on 这个开关，VolumeMan 本身保留，音量增强保留；没有这个 APP 时脚本跳过。不要为了屏保卸载 VolumeMan 或关闭音量增强。

## 第一次开机后的功能检查

- Square Home 使用的是公开包内经过隐私清理的当前实机布局。
- iControl 初始状态是退出登录、无遥控器；登录后要重新添加或学习自己的遥控器。
- Wi-Fi、蓝牙、扬声器、vendor 红外、FUSE 外部存储和 Download 来自此前已验证的 A1260 Android 11 boot/system 与配置路径。
- Android 标准 Consumer IR API 显示不支持属于已知现象；A1260 红外走 vendor 节点和 iControl 路径。
- 不要停用 Android 11 的 MediaProvider、DocumentsUI 或下载组件。图库在这个无摄像头遥控器上默认停用，不会停用文件、媒体提供程序或下载功能。

## 遇到问题时

### SP Flash Tool 显示 PMT changed

立即停止。A1260 的完整 scatter 路径已实测会在 DA 100% 后报 PMT changed，且没有写入分区。

关闭当前操作，重新确认只使用 Advanced Mode > Write Memory，只填写本说明给出的 Region、起始地址、镜像和长度。不要改分区表，不要选 Firmware Upgrade，不要选 Format All + Download。

### 屏保没有自动出现

先确认设备正在充电、屏保已启用，并且连续约 30 秒没有触摸或按键。若安装了 VolumeMan，打开“更多 → 输出源”确认输出源开关是关闭；没有 VolumeMan 时跳过。不要卸载工具，也不要关闭音量增强。

如果仍没有进入，先确认 2-首次开机配置.cmd 或 3-更新现有设备.cmd 已完成，再检查屏保组件是否为 io.github.choumax.a1260clock/io.github.choumax.a1260clock.ClockDreamService。不要下载未知屏保 APK 或手工拼接其他系统设置。

### Recovery 菜单能进，但没有 ADB

这是预期限制。A1260 原厂 recovery 的 ADB 行为不等于 Android 11 的公开刷机接口；公开包不提供自制 Recovery ZIP，也不建议在 recovery 中选择 Apply update 安装未知 ZIP。

如果 boot 和 system 的回读已经通过，退出 recovery，按电源键正常启动 Android。不要因为 recovery 没有 ADB 就继续写其他分区。

### Android 启动后电脑看不到 ADB

先等待系统进入桌面，再执行 adb devices。检查 USB 线、驱动和设备管理器中的 MediaTek/Android 设备，然后拔插一次 USB。设备未进入桌面、未完成首次启动或没有弹出 USB 调试授权时，不要执行写入命令。

如果始终没有 ADB，不要连续换刷多个镜像。回到“回滚”一节，只使用你自己在刷机前制作并核对 SHA-256 的 system/boot 备份。

### 写入后黑屏、卡在开机画面或反复重启

先长按电源键约 10 秒尝试一次完整关机，再重新开机。若仍不能进入桌面，停止继续试错。确认回读文件与发布 SHA-256 一致；若回读不一致，不能把问题归因于 Android 本身。

### 误在已有设备运行了首次配置

2-首次开机配置.cmd 会清除 Square Home 和 iControl 的现有应用数据。公开包只提供干净 profile，不能恢复你的个人账号、token、遥控器、按钮、场景或学习码。只能使用你自己的备份或重新登录、添加和学习；后续保留已有状态的更新请使用 3-更新现有设备.cmd。

## 回滚

公开包不提供原系统备份。每位用户都应在刷机前，从自己的 A1260 制作 system 和 boot 的完整备份，并在电脑上保存文件大小与 SHA-256。没有自己核对过的备份时，不要进行回滚，也不要从第三方下载 system 或 boot。

回滚使用同一条 Write Memory 路径。自己的 system 备份使用 Region=EMMC_USER、起始地址 0x0A800000、长度 0x60000000；自己的 boot 备份使用 Region=EMMC_USER、起始地址 0x01D20000、长度 0x01000000。每次只选择一个文件，写完后用 Readback 回读同一段，并确认回读 SHA-256 与自己的备份完全一致，才可以开机。

回滚也会改闪存；回读不匹配时保持关机，不要继续尝试。应用账号和个人遥控器数据只能由使用者自己的备份或重新登录恢复。

## 已知限制和验证边界

- 这是 A1260 单槽 eMMC 设备，没有 A/B 槽位切换，也没有已验证的 OTA 回退流程。
- 本说明只允许按指定地址写 boot、system，其他分区不要勾选或填写。
- 不提供可公开发布的 recovery 镜像，也没有已验证的 Recovery/ADB 或 OTA 回退流程。
- boot/system 镜像是此前从当前 A1260 分区精确回读并核对过的副本；v1.1.0 沿用它们，本次没有声称重新编译或重新刷写。
- Wi-Fi、蓝牙、扬声器、厂商红外、FUSE、Download、桌面和旧屏保基础能力来自此前实机验证。大字时钟显示效果、DreamService 和基座自动进入也已在当前设备确认。
- 本轮没有从空白设备完成 v1.1.0 全包刷写验收。首次配置清空并恢复 clean profile 没有在源设备执行；3-更新现有设备.cmd 的实机结果由主线另行验证。
- 大字时钟是显示适配，不是完整原厂屏保 APK 恢复。原厂完整 APK、DEX、分析文件和缓存不在公开包内；字体取自本机旧固件，不作为原创字体或 MIT 许可发布。
- iControl 和 Square Home 随包提供的是隐私清理后的初始状态；账号、token、遥控器、按钮、场景、空调状态、学习码、蓝牙配对和 Wi-Fi 凭据必须由使用者重新设置。

只要任一步的文件大小、SHA-256、SP Flash Tool 进度或 Readback 结果不符合本说明，就停止刷写并保留设备当前状态。
