# 第三方应用和权利说明

本固件不包含小智 AI，也不会改动设备上自行安装的小智。

v1.1.0 的 APK 分为两类：apps/ 下的大字时钟显示适配，以及 profile/ 下用于新机干净初始状态的 Square Home 和 iControl。随包提供 APK 不代表本项目取得其源码、商标或额外授权；第三方权利仍归各自权利人。

## 大字时钟显示适配

- 文件：apps/A1260-LegacyClock-0.1.0.apk
- 包名：io.github.choumax.a1260clock
- 大小：35,712 字节
- SHA-256：26d154e955b04784946973a472564abb4bfe94dfd563ed0bee427dd9381628f7
- 屏保组件：io.github.choumax.a1260clock/io.github.choumax.a1260clock.ClockDreamService

这是一个独立的 Android 11 DreamService，只做白字黑底、大时间、完整日期和星期的显示适配。它不是旧固件完整原厂屏保 APK 的恢复。大字时钟使用的字体取自本机旧固件；该字体不是本项目原创字体，不能称为原创字体，也没有在本项目下声明 MIT 许可。字体及旧固件相关材料的权利按其各自权利归属保留。

旧固件屏保的完整 APK、DEX、分析文件和缓存不在公开包内。提供这个显示适配 APK 不代表取得旧固件应用的源码、系统 UID、隐藏 API 或其他授权。

## Square Home

- 文件：profile/apks/com.ss.squarehome2/base.apk
- 版本：Square Home 2.1.14
- 包名：com.ss.squarehome2
- 官方页面：https://play.google.com/store/apps/details?id=com.ss.squarehome2

APK 从 A1260 实机导出，保留原开发者签名，没有修改 APK 内容。公开 profile 只保留当前实机的两套桌面布局和显示设置；首次配置会清掉该应用已有数据并恢复这份干净布局。应用名称、商标和著作权归 Square Home 及其权利人所有。本仓库公开 APK 是为了恢复 A1260 的既有桌面环境，不代表获得应用源码或额外授权，也不代表本项目与开发者存在合作关系。

## iControl

- 文件：profile/apks/com.tiqiaa.icontrol/base.apk
- 版本：iControl 5.2.5
- 包名：com.tiqiaa.icontrol
- 官方页面：https://play.google.com/store/apps/details?id=com.tiqiaa.icontrol

APK 从 A1260 实机导出，保留原开发者签名，没有修改 APK 内容。公开 profile 已清空账号、token、5 个遥控器、193 个按键、26 条红外码、6 个场景和空调状态；首次配置会清掉该应用已有数据并恢复退出登录、无遥控器的干净状态。用户必须自行登录，并添加或学习自己的遥控器。应用名称、商标和著作权归 iControl 及其权利人所有；公开 APK 不代表获得源码或额外授权，也不代表本项目与开发者存在合作关系。

Square Home 和 iControl 的个人账号、token、遥控器、按钮、场景、空调状态、红外学习数据、Wi-Fi 凭据和蓝牙配对记录都不属于公开发布内容。旧机更新入口只安装大字时钟并应用屏保修正，不恢复这份 clean profile，因此会保留设备上已有的桌面和 iControl 数据。

如果任一开发者要求移除公开 APK，应从 Release 删除对应文件，并把教程改为从官方页面安装；脱敏配置仍须与相同包名和兼容签名配套使用。任何第三方许可证、商标和著作权条件继续适用。
