# v1.1.0

这是 ALC/AIC A1260（MT6580、Android 11）的非官方完整固件包。

[v1.1.0 下载地址](https://github.com/ChouMax-1989/alc-a1260-android11/releases/tag/v1.1.0)

本固件不包含小智 AI，也不会改动设备上自行安装的小智。

## 本次发布

- v1.1.0 继续提供完整 boot/system 刷机包。两个镜像沿用此前在当前 A1260 上验证过的精确回读副本；本次没有声称重新编译、重新刷写或从空白设备完成整包验收。
- 新增大字时钟显示适配 APK A1260-LegacyClock-0.1.0，包名 io.github.choumax.a1260clock，大小 35,712 字节，SHA-256 为 26d154e955b04784946973a472564abb4bfe94dfd563ed0bee427dd9381628f7。
- 新屏保使用 io.github.choumax.a1260clock/io.github.choumax.a1260clock.ClockDreamService。充电时闲置约 30 秒进入，显示白字黑底的大时间、完整日期和星期。显示效果及基座自动进入已由用户在当前设备确认。
- 若设备有 VolumeMan，且“更多 → 输出源”已打开，修正会关闭 output_source_on；该工具每约 0.5 秒更新音频并重置闲置计时。修正不卸载 VolumeMan，保留音量增强设置；需要改输出源时会先暂停该工具，之后可重新打开使用增强；设备没有这个 APP 时跳过。
- 保留首次配置入口 2-首次开机配置.cmd，并提供旧机更新入口 3-更新现有设备.cmd。首次配置会恢复干净的 Square Home/iControl profile 并清掉这两个应用已有数据，只适用于新机或明确重装；更新入口只安装大字时钟并应用屏保修正，保留已有桌面、遥控器和账号，不需要重刷。

## 沿用的已验证镜像和刷写位置

| 分区 | Region | 起始地址 | 长度 | 文件大小 | SHA-256 |
| --- | --- | --- | --- | ---: | --- |
| boot | EMMC_USER | 0x01D20000 | 0x01000000 | 16,777,216 字节 | 52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878 |
| system | EMMC_USER | 0x0A800000 | 0x60000000 | 1,610,612,736 字节 | 984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385 |

刷写仍只使用 SP Flash Tool Advanced Mode > Write Memory。每个区间写完后都必须按同一区域和地址完整 Readback，再核对上表 SHA-256。不要选择 Firmware Upgrade 或 Format All + Download。

## 已确认的范围

- boot/system 来源于当前正常运行实机的完整分区副本，长度与设备端、电脑端 SHA-256 一致；system 只读检查通过。
- Wi-Fi、蓝牙、扬声器、厂商红外、FUSE 外部存储、Download、桌面和旧屏保基础能力此前已在 A1260 实机使用通过。
- 新大字时钟显示、DreamService 组件和通电基座自动进入已在当前设备确认。
- Square Home 2.1.14 的两套桌面布局和显示设置已做隐私清理；iControl 5.2.5 已清除账号、token、5 个遥控器、193 个按键、26 条红外记录、6 个场景及空调状态。
- 首次配置用的 profile 做过静态、文件集合、数据库关系和哈希检查。

## 不把这些结果写成已通过

- 本轮没有从空白设备完整刷写并验收 v1.1.0。
- 首次配置清空并恢复 clean profile 没有在源设备执行，以免破坏源机个人数据。
- 旧机更新脚本已在当前设备执行通过，6份既有数据文件哈希不变；隔离Android夹具也验证了VolumeMan单键修改及回滚。此项不代表首次清空profile或整机重刷已实测。
- 大字时钟是显示适配，不是完整原厂屏保 APK 恢复。原厂完整 APK、DEX、分析文件和缓存不进公开包。
- 字体取自本机旧固件，只用于显示适配；不称为本项目原创字体，也不声明 MIT 许可。

## 没有放进包里

- Wi-Fi 密码、蓝牙配对记录、设备序列号、账号 token、红外学习数据。
- NVRAM、nvdata、protect、userdata、整机备份、日志、截图、缓存和崩溃数据。
- 其他设备的个人应用数据、原厂完整屏保 APK/DEX 及其分析产物。
- Recovery 镜像、SP Flash Tool、驱动、platform-tools 和原厂回滚镜像。

旧 v1.0.0 和 v1.0.1 会在 v1.1.0 下载验证后撤下，后续只推荐 v1.1.0。
