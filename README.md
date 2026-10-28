# 星越L银河助手 · 无 HUD 版（GalaxyLink Toolkit - No HUD）

> 面向吉利星越 L 车机的 Android 车机助手工具（无 HUD 版本）。**本仓库仅用于公开发布安装包**，不包含工程源码。

[![GitHub Release](https://img.shields.io/github/v/release/joyli/galaxylink-nohud-release?label=Release)](https://github.com/joyli/galaxylink-nohud-release/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## 这是什么

「星越L银河助手」是面向吉利星越 L 等车机平台的系统级工具箱应用，用于集中管理车辆相关的自动化与设置项。

**本仓库发布的是「无 HUD 版本」**：相比完整版，移除了 HUD / 歌词 / 频谱显示相关功能，体积更小、依赖更少，适合不需要 HUD 叠加显示的用户。

## 功能一览

| 模块 | 说明 |
| --- | --- |
| 状态 | 设备概览、实时仪表（八环）、应用必要权限状态（9/9 全绿提示）、设备信息、设置文件管理 |
| 车辆信息 | 左右双列等高布局，含胎压监测（kPa/℃）、灯光（含扩展灯位）、车窗/天窗/遮阳帘百分比、座椅、能源动力、环境、车身、制动与驾驶辅助、车身姿态等 |
| 自动化 | 座位记忆、快捷指令、按键配置、开机自启应用与返回主页策略 |
| 悬浮球 | 桌面悬浮球，支持尺寸（56/64/80/96/128 px）、图标与形状自定义 |
| 声效 | 提示音、锁车音等音效选择 |
| 全景 | 全景影像相关设置 |
| 通用 | 界面显示、悬浮球外观、更新检查等系统级设置 |

## 下载

前往 [Releases 页面](https://github.com/joyli/galaxylink-nohud-release/releases) 下载，历史版本同样保留可下载。

> **发布策略（v0.0.0.88 起生效）**
>
> - **双端 Releases 页面：每个版本永久保留，不删除**（GitHub / Gitee 均已发布的版本按版本迭代累加）。
> - **本地仓库 `releases/` 目录：只保留最新版本安装包**，旧版安装包不在仓库内累积（避免仓库体积膨胀）。
> - 变更文字记录见 [CHANGELOG.md](CHANGELOG.md)，亦永久保留。

| 版本 | 渠道 | 最低系统 | 说明 |
| --- | --- | --- | --- |
| **v0.0.0.88（最新）** | beta | Android 9 (API 28) | 修复状态页动力电池容量不刷新（SOC 0.1% 量纲解码修正 + getter 优先级调整）；含顶栏『关于软件』版权弹窗、车辆信息页 CarSignal 全量扩充、胎压 kPa/℃、天窗与遮阳帘百分比 |

安装包为 **Debug 签名**，可直接安装：

```bash
adb install -r galaxylink-toolkit-nohud-v0.0.0.88-beta-debug.apk
```

SHA-256：`44ec6f79a9cb47aa67c66029f3af7b8522cbfd815282d209320bae3d78e8247a`

> 需要车机已授予相应权限（悬浮球、后台运行、无障碍等）。首次安装后可在「通用 → 界面显示」中开启相关页面入口。

## 版本记录

详见 [CHANGELOG.md](CHANGELOG.md)。

## 兼容性

- 最低系统版本：**Android 9（API 28）**
- 目标平台：吉利星越 L 车机（GalaxyLink 框架）
- 架构：APK 内含 `arm64-v8a` / `armeabi-v7a` / `x86` / `x86_64` 四种 ABI，通用安卓模拟器亦可安装
- 部分功能（胎压等）依赖车机框架接口，在非车机环境下会显示为不可用

## 说明与免责

- 本仓库**仅发布二进制安装包**，源码另行管理，不在此公开。
- 安装包为测试用途的 Debug 构建，正式使用请留意版本说明。
- 使用中如遇问题，可附上 `adb logcat` 日志反馈。

## 许可

安装包随主工程许可协议分发，详见 [LICENSE](LICENSE)。
