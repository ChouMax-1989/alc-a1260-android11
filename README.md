# ALC A1260 Android 11 实机固件

我手里这台 ALC A1260 原来跑的是定制 Android 8。机器还能用，但系统太老，很多新应用已经不支持。前后折腾了不少次，最后把 Android 11 跑稳了：Wi-Fi、蓝牙、扬声器、红外、下载和屏保都能正常用。

这次放出来的 `system` 和 `boot` 是从这台正常使用的 A1260 上完整读出来的，不是随便找的 MT6580 通刷包。Square Home、音量君和遥控精灵也一起整理好了。桌面布局保留，遥控精灵里的账号和旧遥控器已经清空，刷完的人自己登录、自己学习红外就行。

## 下载固件

- [v1.0.1 发布页](https://github.com/ChouMax-1989/alc-a1260-android11/releases/tag/v1.0.1)
- [直接下载完整 ZIP](https://github.com/ChouMax-1989/alc-a1260-android11/releases/download/v1.0.1/ALC-A1260-Android11-v1.0.1.zip)
- [下载 SHA-256 文件](https://github.com/ChouMax-1989/alc-a1260-android11/releases/download/v1.0.1/ALC-A1260-Android11-v1.0.1.zip.sha256)

完整包大小：733,065,028 字节  
SHA-256：`81a47cc8a40bb21abbc925002d20c99e883585783d6a039e31e01f5acf8a1c50`

## 需要的工具

- [Android platform-tools 官方页面](https://developer.android.com/tools/releases/platform-tools)
- [Windows platform-tools 官方直链](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)
- [MediaTek PreLoader USB VCOM 驱动：Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=MediaTek%20PreLoader%20USB%20VCOM)
- [SP Flash Tool：MediaTek 官方工具门户](https://online.mediatek.com/English/Tool)

我实际使用的是 SP Flash Tool v5.2032。其他版本也许能用，但先确认界面里有 `Advanced Mode > Write Memory`。不要加载陌生的 scatter、preloader 或 DA。

## 最简单的刷法

### 1. 解压固件

完整解压 ZIP。下载过程不稳定，或者文件经过网盘转存时，可以双击 `1-检查固件.cmd` 检查一次；这一步只检查文件，不会刷机，可以跳过。

Windows 自带的 PowerShell 5.1 就能运行检查，不需要安装 `pwsh`。手动命令是：

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\verify-package.ps1
```

看到“全部通过”表示文件没有损坏。

### 2. 写 boot

1. 打开 `flash_tool.exe`。
2. 切到 `Advanced Mode`。
3. 点 `Window > Write Memory`。
4. `Region` 选 `EMMC_USER`。
5. `Begin Address` 填 `0x01D20000`。
6. 文件选 `firmware\a1260-boot-android11-usap-v1.img`。
7. 点 `Write Memory`。
8. 把 A1260 完全关机，再直接插 USB。不要按音量键。
9. 看到绿色完成标志后拔线。

### 3. 写 system

仍在 `Write Memory` 页面：

1. `Region` 还是 `EMMC_USER`。
2. `Begin Address` 改成 `0x0A800000`。
3. 文件选 `firmware\a1260-android11-system-current.img`。
4. 点 `Write Memory`。
5. 设备完全关机，再插 USB。
6. 看到绿色完成标志后拔线开机。

就写这两个分区。不要选 `Download`、`Firmware Upgrade` 或 `Format All + Download`，也不要写 recovery、preloader、NVRAM、userdata 等其他分区。

### 4. 第一次开机

第一次开机会慢一些。进入 Android 11 桌面后打开 USB 调试，把下载好的 `platform-tools` 文件夹放到固件包根目录，然后双击 `2-首次开机配置.cmd`。脚本自己会先检查需要的文件，不用另外运行 `-StaticValidate`。

手动命令是：

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\setup\post-install.ps1
```

脚本会完成这些事：

- 安装 Square Home 2.1.14 和遥控精灵 5.2.5；
- 恢复现在这套 Square Home 桌面布局；
- 安装红外和 FUSE 存储修复；
- 关闭电话、通讯录、图库等遥控器用不到的应用；
- 打开系统屏保；
- 重启并检查红外、Wi-Fi、蓝牙、Download 和桌面。

遥控精灵打开后是退出登录、没有遥控器的干净状态。登录自己的账号，添加设备，再学习自己的红外即可。

## 想更稳一点：写完做 Readback

简单刷法到绿色完成标志就可以继续。如果这是第一次刷这类机器，建议多做一次回读校验：

| 分区 | 起始地址 | 长度 | 正确 SHA-256 |
|---|---|---|---|
| boot | `0x01D20000` | `0x01000000` | `52adc739311293263f5e2b322628a6768385b88c65351b1203eae1c3f0982878` |
| system | `0x0A800000` | `0x60000000` | `984faeb4df4415e02b1d791158392a091abe67f815d7892ac14f46cd80662385` |

在 SP Flash Tool 的 `Readback` 页面按上表读取完整区间。回读文件的 SHA-256 一致，说明写入没有断线或损坏。

## 常见问题

### 提示 PMT changed

说明你走到了 `Download` 流程。关掉它，回到 `Advanced Mode > Window > Write Memory`，只按上面的两个地址写。

### 关机插线没反应

先检查 MediaTek PreLoader USB VCOM 驱动和数据线。A1260 不需要按音量键，也不用拆机短接。先在 SP Flash Tool 里点写入，再让设备完全关机后插线。

### 刷完卡开机或黑屏

先检查文件 SHA 和写入地址，不要连续换包乱刷。刷机前应备份自己设备的 boot 和 system；需要回滚时，用同一组地址写回自己的备份。公开包不带原厂备份，也不要拿别人的 NVRAM、protect 或 userdata 来刷。

## 隐私处理

公开包保留 Square Home 当前桌面布局和显示设置。以下内容已经删除：

- Wi-Fi 密码和网络名称；
- 蓝牙配对记录；
- 设备序列号和唯一标识；
- 遥控精灵的 2 个账号、token；
- 5 个旧遥控器、193 个按键、26 条红外码、6 个场景；
- NVRAM、nvdata、protect、userdata、日志、缓存和截图原始数据。

## 实机效果

下面都是完成升级后的 A1260 实机截图。遥控截图只是展示功能界面；公开配置已经清空旧账号和遥控器。

<img width="240" alt="Square Home" src="https://github.com/user-attachments/assets/4ff43c99-f5ee-48f8-b0e7-b432a7558384" />
<img width="240" alt="Android 11" src="https://github.com/user-attachments/assets/f14db74e-ae98-46eb-8a88-bb63092cf90e" />
<img width="240" alt="遥控精灵" src="https://github.com/user-attachments/assets/d42ef178-4576-4cda-a31e-8a27b0fa552e" />
<img width="240" alt="音量君" src="https://github.com/user-attachments/assets/044f76bb-fa02-43d6-8435-f3a1bf2d0c8c" />
<img width="240" alt="遥控器列表" src="https://github.com/user-attachments/assets/73b8cd96-e3cf-4cc2-b62f-96ce35bc41eb" />
<img width="240" alt="添加遥控器" src="https://github.com/user-attachments/assets/0b0301a6-c5db-409b-93e2-574776e22cfd" />
<img width="240" alt="智能场景" src="https://github.com/user-attachments/assets/a094a0da-69e0-4252-951a-e05b35e3c4e3" />

这是 ALC/AIC A1260 的非官方固件，只按这个型号和本文地址使用。
