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
| 车辆信息 | 左右双列等高布局，含胎压监测（kPa/℃）、灯光（近光/远光/位置灯/雾灯/日行灯等，含状态去抖）、车窗/天窗/遮阳帘百分比、座椅、能源动力、环境、车身、制动与驾驶辅助、驾驶状态（驾驶模式/挡位/手刹/AutoHold） |
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
| **v0.0.1.11（最新）** | beta | Android 9 (API 28) | 修复「调试」页点击无反应：调试路由改为常驻 NavGraph（原按开关条件注册，图中无该目的地导致 navigate 抛异常且被静默吞掉）；侧栏导航失败补日志 |
| v0.0.1.10 | beta | Android 9 (API 28) | 修复「调试」页点击后打不开：侧栏可见路由列表的缓存 key 漏掉调试页开关，开启后入口不刷新 |
| v0.0.1.9 | beta | Android 9 (API 28) | 调试页可隐藏（连点侧栏「通用」5 次切换显隐，首次安装默认隐藏）；修复 Gitee/GitHub 检查更新 404（更新仓库名 galaxylink-releases → galaxylink-nohud-release、选型取最大版本）；全局等比缩放（按真实分辨率换算 density，任意车机屏 UI 比例一致） |
| v0.0.1.6 | beta | Android 9 (API 28) | 「检测更新」面板从「通用」页迁入「状态」页并重命名；移除「启动自动检查更新」改为手动点击确认；移除 ExoPlayer 改用系统 MediaPlayer，APK 14.0 MB → 12.0 MB |
| v0.0.1.4 | beta | Android 9 (API 28) | 新增前后超声波雷达（泊车辅助 PAS）探测日志（PDC 诊断 + 前/后 × 内/外左/右 8 路传感器）；修复前碰撞预警 FCW 灵敏度取不到的问题（改用 getCllsnFwdWarn） |
| v0.0.1.3 | beta | Android 9 (API 28) | beta 版本号改为「小时点 + 组内序号」分组（第四段满 100 进位第三段），序号 102 → 0.0.1.2、本版 → 0.0.1.3，版本号连续不跳号 |
| v0.0.0.102 | beta | Android 9 (API 28) | 在线更新地址改为 `?api=1` 并适配接口扁平响应结构（顶层 tag + apks），保留旧结构兼容；补充真实接口样本单测 |
| v0.0.0.101 | beta | Android 9 (API 28) | 移除启动页二维码资源；启用体积压缩（R8 full mode + so 压缩 + 资源裁剪 + 移除 ui-tooling），APK 15.0 MB → 13.8 MB |
| v0.0.0.99 | beta | Android 9 (API 28) | 车辆信息页全面修正：前碰撞/倒车预警改用功能状态、灯光合并并去抖、机油与油量口径纠正、车辆总里程改走 PA 通道、挡位映射 bug 修复；新增驾驶模式/挡位/手刹/AutoHold 检测 |
| v0.0.0.90 | beta | Android 9 (API 28) | 车辆信息页仅展示可获取值的项，无数据项不再渲染（去除「无数据」占位） |
| v0.0.0.88 | beta | Android 9 (API 28) | 修复状态页动力电池容量不刷新（SOC 0.1% 量纲解码修正 + getter 优先级调整）；含顶栏『关于软件』版权弹窗、车辆信息页 CarSignal 全量扩充、胎压 kPa/℃、天窗与遮阳帘百分比 |

安装包为 **Debug 签名**，可直接安装：

```bash
adb install -r galaxylink-toolkit-nohud-v0.0.1.11-beta-debug.apk
```

SHA-256：`4a3620ec0cf1f8ed51bdaac7ead3f34954521d0f709de3c64ea48999ebc3937a`

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
