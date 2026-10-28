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

前往 [Releases 页面](https://github.com/joyli/galaxylink-nohud-release/releases) 下载最新版本。

| 版本 | 渠道 | 最低系统 | 说明 |
| --- | --- | --- | --- |
| v0.0.0.85 | beta | Android 9 (API 28) | 顶栏『检查更新』改为『关于软件』，点击弹出 WebView 版权说明页（支持上下滑动） |
| v0.0.0.84 | beta | Android 9 (API 28) | 车辆信息页按 CarSignal 全量扩充（机油/12V蓄电池/油量/续航/里程/保养/环境/车身/扩展灯光/制动辅助/姿态），胎压加 kPa/℃、天窗与遮阳帘按百分比 |
| v0.0.0.82 | beta | Android 9 (API 28) | 保养信息文案统一为「重置保养信息」 |
| v0.0.0.81 | beta | Android 9 (API 28) | 工程合并单模块、连接层统一、图标 WebP、开启资源缩减 |
| v0.0.0.78 | beta | Android 9 (API 28) | 车辆信息灯光显示状态值、车窗按 100% 行程、补齐信号日志 |
| v0.0.0.77 | beta | Android 9 (API 28) | 在线更新检测改用 HTTPS |
| v0.0.0.76 | beta | Android 9 (API 28) | 在线更新检测改用官方解析接口 |
| v0.0.0.75 | beta | Android 9 (API 28) | 修复 Android 9 启动后界面不显示问题 |

安装包为 **Debug 签名**，可直接安装：

```bash
adb install -r galaxylink-toolkit-nohud-v0.0.0.75-beta-debug.apk
```

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
