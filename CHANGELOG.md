# 更新日志

本文件记录「星越L银河助手 · 无 HUD 版」的公开版本变更。

> **产物策略（v0.0.0.88 起生效）**
>
> - **双端 Releases 页面：每个版本永久保留，不删除** —— GitHub / Gitee 已发布的版本按版本迭代累加，历史版本可随时下载。
> - **本地仓库 `releases/` 目录：只保留最新版本安装包** —— 旧版安装包不在仓库内累积，避免体积膨胀。
> - 本文件的变更记录永久保留。
>
> 当前最新：**v0.0.1.14** → `releases/v0.0.1.14/galaxylink-toolkit-nohud-v0.0.1.14-beta-debug.apk`

## v0.0.1.14 (beta)

- **修复前后雷达探测日志始终 0 条**：雷达探测日志由 `EcarxCabinSignalController.pollRadar()` 产出（1.5s 轮询，状态变化或15s 心跳时落盘），但该控制器此前**仅在打开「车辆信息」页时创建**，而该页默认隐藏（需连点侧栏「状态」5 次才显示），导致控制器从未实例化、`pollRadar` 从未执行，日志里连 `PAS manager` 相关记录都没有。现参照车门/行驶信号控制器的方式，在 `GalaxyLinkApplication` 中改为**全局常驻**（`obtain` 为线程安全单例，与页面共用同一实例，不会重复创建），雷达日志与 PDC 诊断从应用启动起即可记录。
- 验证：v0.0.1.13 实测日志（2026-10-30 06:43~ 06:47）确认 `radar detection` 0 条、且无任何 `GalaxyLinkCabinSignal` tag 记录，仅有 DoorSignal / DriveSignal / Trunk / Ignition 四个控制器，佐证座舱信号控制器当时未启动。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.14-beta-debug.apk` | `9b599f3b1e66201cf87ca21a1232f1c46109538179878a897d71aaa52bd83fd3` |

## v0.0.1.13 (beta)

- **左侧 dock 栏 6 个入口一屏全部显示（无需滚动）**：dock 栏原先按固定规格渲染（单项高 90dp = 纵向内边距 22dp×2 + 图标 46dp），6 项 + 间距共需 604dp，在 1920×720（逻辑高 600dp，dock 可用约 508dp）上放不下，底部入口被裁需滚动才能看到。现改为**按容器可用高度自适应**：高度不足时自动切换紧凑规格（纵向内边距 22→11dp、图标 46→34dp、强调条 56→38dp、字号 22→17sp），1920×720 下实际总高降至 400dp，6 个入口全部可见；1920×1080 等高度充足的屏幕仍保持原大规格，视觉不变。
- **隐藏入口顺序调整**：「车辆信息」与「调试」默认隐藏，现移至「副驾迎宾」之后（dock 顺序：状态 → 通用 → 常用 → 自动化 → 方控 → 副驾迎宾 → 车辆信息 → 调试），默认可见的 6 个常规入口连续排列在最上方。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.13-beta-debug.apk` | `7b1422939e73f5f972881564d31c9b465795beef51b35613281800223f1adf4c` |

## v0.0.1.12 (beta)

- **修复 1920×720 车机显示不正常（内容裁切）**：全局等比缩放原按**仅宽度**换算 density（`widthPx / 1320`），在 1920×720（宽高比 2.67，比 2608×1200 的 2.17 更扁）上把逻辑画布压成 `1320×495dp`，扣掉顶栏与内边距后纵向内容区仅约 380dp，导致「设备概览」面板底部被裁、侧栏「常用」以下入口不可见。现改为**宽高双向取更严格的一侧**（contain 语义，设计基准 1320×600dp）：1920×720 的 density 由 1.455 降为 1.200，逻辑画布 `1320×495dp` → `1600×600dp`（纵向 +21%），内容完整显示；2608×1200 / 1920×1080 / 1920×1200 的 density **保持不变**，既有观感零影响。
- **缩放随车机分辨率变化实时重算**：缩放基准改由 `LocalConfiguration` 驱动（原先在 `onCreate` 一次性读取 `DisplayMetrics`），用户在车机系统里修改显示分辨率后配置变化即自动重算，无需重启应用。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.12-beta-debug.apk` | `9399636cb78f96c62b1f8324cdc96816bf74a44b2ec97722e7fadd17c96a38c1` |

## v0.0.1.11 (beta)

- **修复「调试」页点击无反应**：调试页路由改为**常驻 NavGraph**。原先在 NavHost 的 builder 内按 `if (BuildConfig.DEBUG && generalSettings.debugPageEnabled)` 条件注册路由，而 NavHost 的 builder 被 `remember(route, startDestination, builder)` 缓存、图仅在启动时构建一次（builder 捕获的是 State 实例，引用不变），导致开关开启后图中并无 `debug` 目的地，点击时 `navigate("debug")` 抛 `IllegalArgumentException`，又被外层 `runCatching` 静默吞掉——表现为「点击无反应、无崩溃、无日志」。现改为路由常驻注册，显隐由侧栏 `visibleRoutes` 与页面内守卫（`BuildConfig.DEBUG && debugPageEnabled`）控制。
- **侧栏导航失败补日志**：`runCatching` 增加 `onFailure` 分支，写入 `GalaxyLinkShell` 错误日志（`侧栏导航失败: route=xxx`），避免后续同类问题再次「点了没反应」却无迹可循。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.11-beta-debug.apk` | `4a3620ec0cf1f8ed51bdaac7ead3f34954521d0f709de3c64ea48999ebc3937a` |

## v0.0.1.10 (beta)

- **修复「调试」页点击后打不开**：侧栏可见路由列表 `visibleRoutes` 的 `remember` 缓存 key 只包含 `vehicleInfoPageEnabled`，遗漏了 `debugPageEnabled`。导致连点「通用」5 次开启调试页后，开关状态虽已写入 DataStore，但侧栏列表不重算、「调试」入口不出现、点击无响应。补齐 key 后入口即时出现，点击即可进入调试界面。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.10-beta-debug.apk` | `db10fa30b173452762dc953432907f40cc83909fe0f95af4652637327cbd5dd1` |

## v0.0.1.9 (beta)

- **调试页可隐藏（连点侧栏「通用」5 次切换显隐，首次安装默认隐藏）**：侧栏「调试」入口受 `GeneralSettings.debugPageEnabled` 控制（DataStore 持久化，默认 `false`）；连续点击侧栏「通用」达到 5 次（`SecretTapCount`，相邻两次间隔 ≤ 2s）切换显隐并 Toast 提示，复用既有「连点状态切车辆信息页」的机密点击逻辑。NavHost 的调试页路由同步以 `debugPageEnabled` 守卫，隐藏时自动重定向到首个可见页面。
- **修复 Gitee / GitHub 检查更新 404**：更新客户端仓库名由 `galaxylink-releases` 修正为 `galaxylink-nohud-release`（与 AGENTS.md 一致）；更新选型由「取第一个匹配项」改为「取版本号最大的一条」，规避 Gitee 旧→新排序误取旧版（如 `v0.0.0.88`）。
- **全局等比缩放（按真实分辨率适配）**：`MainActivity` 启动时读取屏幕真实宽度（px），按设计宽度 1320dp 换算全局 density 并注入 `LocalDensity`，任意车机屏 UI 元素比例与单屏可见内容量一致，不再受系统密度差异导致单/双栏布局乱跳。

> 注：v0.0.1.7（Gitee 404 修复）、v0.0.1.8（等比缩放）仅在本地构建、未单独对外发布，本版合并承载其全部变更。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.9-beta-debug.apk` | `41ca08c471a0e2f4ebf76a1ca1c265a5e1dc4ea339a4ab686b9e4d30a80c82c8` |

## v0.0.1.6 (beta)

- **「检测更新」面板从「通用」页迁入「状态」页并重命名**：原「关于与更新」面板移出通用设置，改置于状态页，标题统一为「检测更新」，更新来源选择、检查更新按钮与状态提示保留。
- **移除「启动自动检查更新」，改为手动确认**：删除该开关及启动静默检查逻辑（`GalaxyLinkAppUpdateCoordinator.onAppLaunch` / `setAutoCheck` / `autoCheck` 字段，以及 `GalaxyLinkApp` 启动检查调用），用户需自行点击「检查更新」按钮确认升级。
- **移除 ExoPlayer 改用系统 MediaPlayer（体积优化）**：迎宾视频投影、迎宾编辑预览由 `androidx.media3` 改为 `android.media.MediaPlayer`，移除 `media3-exoplayer` / `media3-ui` 依赖，APK 体积约 14.0MB → 12.0MB（与上一工作树 v0.0.1.5 合并发布）。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.6-beta-debug.apk` | `1c88f5d13bf2b59c35cdb49133d47a7b03ed5cbedab8518e3fdcf89b16d1dd6c` |

## v0.0.1.4 (beta)

- **新增前后超声波雷达（泊车辅助 PAS）探测日志**：`EcarxCabinSignalController` 新增 `pollRadar()`，每 1.5s 轮询前后泊车雷达信号，状态变化或每 15s 心跳时打点一行（Tag=`GalaxyLinkCabinSignal`）。
  - **PDC 系统诊断**（`CarSignalManager`）：PDC 静音键、超声波声道、输出时长、挂车障碍探测、代客泊车余量、PDC 车型模式。
  - **前后超声波雷达逐传感器**（`ECarXCarPassapManager`，路径 `car_publicattribute → ECarXCarSetManager → getECarXCarPassapManager`）：前/后 × 内左/内右/外左/外右 共 8 路 + 前/后音频告警 + PDC 控制状态/开关 + 传感器故障告警。
- **修复前碰撞预警（FCW）灵敏度取不到的问题**：原硬编码的 `getCllsnAidSnvtySeldSts` 在该车型 JAR 上不存在（日志持续报 `NoSuchMethodException`），改为有效的 `getCllsnFwdWarn`（前碰撞预警等级 0/1/2/3），日志字段 `fcwWarn=` 不再恒为 `null`。
- 经核查非代码缺陷、未做改动的项：驾驶模式管理器 `getECarXCarSetManager`（旧异常来自旧包）、AAOS `CarPropertyManager` 未连接（Ecarx 本就不暴露标准属性服务，已优雅降级）、座椅通风 `rawLevel` 大值（HVAC 解包前原始值）、电池瞬时 `published=null`（连接首帧瞬时）。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.4-beta-debug.apk` | `520ecaae322ccac5bf4ca55096794752018d7f6986981b1b1a15a23a82c69c8e` |

## v0.0.1.3 (beta)

- **beta 版本号改为「小时点 + 组内序号」分组**：第四段满 100 时向前一个「小时点」进位（第三段 +1）。
  - 原 `0.0.0.102` 与新 `0.0.1.2` 表示**同一个序号**（序号 = 第三段 × 100 + 第四段 = 102），
    因此本版本号从 `v0.0.0.102` 顺延为 **`v0.0.1.3`**（序号 103），版本号连续不跳号。
  - 序号与展示对照：`0.0.0.99` → `0.0.1.0` → `0.0.1.1` → `0.0.1.2` → `0.0.1.3` → … → `0.0.1.99` → `0.0.2.0`。
  - 在线更新的渠道判定与版本比较沿用逐段比较逻辑，新旧两种写法均正确（新写法仍为4 段，仍归属beta 渠道）。
  - 补充单测覆盖：跨分组比较（`0.0.1.2` vs `0.0.0.102`）、分组边界（`0.0.2.0` vs `0.0.1.99`）与新格式标签解析。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.1.3-beta-debug.apk` | `e8d4ba6ba0c9cf6fc8acb7dbb504ab37219ffee77ff6f08042b55c50794620cc` |

## v0.0.0.102 (beta)

- **在线更新地址与响应适配**：更新接口地址改为 `https://www.izhuoe.com/galaxylink-release-parser.php?api=1`（原 `?format=json`）。
  - 接口现行返回为**扁平结构**（`success` / `source` / `tag` / `version` / `prerelease` / `notes` / `history` / `apks`），
    与原先按`platforms.*.releases[]` + `latest.downloads[]` 嵌套结构解析的实现不匹配，会导致更新检查取不到安装包。
  - 现改为优先按扁平结构解析：顶层 `tag` 匹配渠道时取 `apks` 中首个 `.apk` 作为下载地址与大小；
    `apks` 仅携带最新版本的安装包，因此不会跨渠道复用（最新为beta 时不会把 beta 包当作 stable 提供）。
  - 旧版嵌套结构解析保留为兼容回退，接口结构回退时仍可工作。
  - 补充真实接口响应样本的单测（`parser_api1_sample.json`），确保解析逻辑与线上实际返回保持一致。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.102-beta-debug.apk` | `a1aa0ecc23545d9e7fe9261b5d4d50c8de0d1120fc2aa0daea08beb5791e96f3` |

## v0.0.0.101 (beta)

- **移除启动页二维码图片**：删除 `startup_qr.jpg`（162 KB）及其启动页展示块，启动页左侧信息区自适应填充。
- **启用安装包体积压缩**：APK 从 **15.0 MB 降至 13.8 MB**（-0.55 MB / -3.8%）。
  - 开启 **R8 full mode**（`android.enableR8.fullMode=true`），启用全模式优化以压缩 dex。
  - **移除 debug-only 的 `compose.ui-tooling` 依赖**（项目内零处 `@Preview` 使用），该库仅供 IDE 预览使用，却会带入大量运行时代码。
  - **native 库改为压缩存储**（`useLegacyPackaging = true`），`.so` 压缩比约 40%~58%。
  - **剔除无用打包文件**（`kotlin_module`、`DebugProbesKt.bin`、`kotlin-tooling-metadata.json` 等 META-INF 条目）。
  - **移除 dex 行号表**（`LineNumberTable`），保留 `SourceFile` 以维持日志中类名可读性。
  - 补充资源保护规则（`-keep class **.R$drawable**`），确保资源缩减不会误删 vector drawable 图标。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.101-beta-debug.apk` | `a21e35603e0b594e11b344c3021f4177c8ce8aabc677c9640d4aae9757844366` |

## v0.0.0.99 (beta)

车辆信息页基于实车日志的全面修正：多个信号此前取错了信号源或误解了枚举语义，导致状态显示错误或整项无值。

### 修复

- **倒车预警 / 前碰撞预警状态显示错误**：此前用 `getCllsnFwdWarn`（前碰撞**预警等级**枚举，0=NoWarning）与 `getRctaIndcnLe`（倒车雷达**指示等级**，0=NoWarning）按开关解读，功能实际开启时显示「关闭」。
  - 前碰撞预警改用**功能状态** `getCllsnMtgtnFctSts`（ON=2 / OFF=1），并新增「碰撞预警灵敏度」行 `getCllsnAidSnvtySeldSts`（未激活/关闭/开启/中/高）。
  - 倒车侧预警改为等级语义（无预警/一级/二级），左右雷达合并取高等级。
- **灯光状态闪烁与不可用**：BCM property 侧外灯属性实测除远光灯外全部 `NO_SIGNAL(-1)`，且多 areaId 事件反复推不同值导致抖动。
  - 改接 cabin 侧 `getExtrLtgSts*` 系列（近光/远光/自动远光/自适应大灯/全天气灯/前后位置灯）为主来源，BCM 侧兜底。
  - 新增 `LightStabilizer` 去抖：非默认值需连续 3 次才发布，关灯（OFF）立即生效，避免状态来回闪烁。
- **机油显示为「200」**：车机 `getEngOilLvl` 上报 0.1% 量纲之外的整数值，现按百分比还原显示（实测 200 → 100%），并新增「机油状态」行（正常/偏低/偏高/需保养）。
- **油量显示为「213 L」**：车机 `getFuLvlIndcdFuLvlValFromFuTbl` 与 `getPercOfFuLvlIndcn` 基准不一致，直接当升数显示明显失真。现以百分比为准、按官方 55L 油箱容量反推升数。
- **车辆总里程无显示**：`getDstTrvld1` 在本车返回无效值，改走 `car_publicattribute` 的 `getPA_Total_Traveled_Distance`（实测 50945 km），并移除「保养剩余时长」改为「车辆总里程」。
- **挡位显示错误（D 档显示 R 档）**：`getPtGearAct` 的真实编码是 `PtGearAct1`（Neut=0、Gear1~12=1~12、Drv=13、Ukwn=14、Rvs=15），此前按 `P=0 R=1 N=2 D=3` 硬编码映射，把 1 档误判为 R 挡。现按真实枚举解码，并优先使用 cabin 侧 `getGearLvrIndcn`（含 P=0/R=1/N=2/D=3 语义）。
- **盖板类信号解码**：油箱盖 / 引擎盖 / 充电口盖改用 ECarX 通用开关编码 `OffOnErrInvalid`（0=关闭/1=打开/2=异常/3=无效），不再把状态码当开关值显示。

### 新增

- **驾驶模式检测**：改走 `car_publicattribute` → `ECarXCarSetManager` → `ECarXCarDrivemodeManager`，读取 Eco / Comfort / Dynamic / Power / Hybrid / Snow / Sand / Mud / Rock / Individual / Adaptive / Pure 各模式的独立选中标志（值 1=选中），页面显示实际生效的模式名。
- **挡位检测**：新增「驾驶状态」卡片，实时显示 P / R / N / D / 手动模式。
- **手刹状态检测**：电子手刹 EPB 支持 `getEpbStsEpbSts`（已拉起/已释放/动态制动中），并以 `getPA_EPBAutoApply` 兜底。
- **AutoHold 状态检测**：改走 `getPA_AutoHoldStatus`，car_signal 侧 `getAutHldSoftSwtEnaSts` 兜底。

### 顺带改进

- PA 通道（`car_publicattribute`）承载总里程 / AutoHold / EPB / 驾驶模式等必需项，修复了「尾门取数失败即连带放弃 PA 连接」导致的连带失联问题，改为持续重试直至就绪。
- 驾驶模式管理器获取失败日志节流至 60 秒一次，避免高频刷屏。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.99-beta-debug.apk` | `638180f02525f49d53db3475b458f43a3c56a673d99814cfcd4bc1c89611fe57` |

## v0.0.0.90 (beta)

- **车辆信息页：仅展示可获取值的项**：结合实车日志，移除了大量车机框架无法返回有效值的 CarSignal 项——原「无数据」占位不再渲染，页面只保留当前车辆真正能取到的信号，避免满屏占位、信息过载。
  - `RawSignalRow`：值为 `null` 时直接不渲染该行。
  - 各取值 helper（开关 / 数值 / 温度 / 油量 / 电池电压 / 外部灯光 / 天窗百分比等）原为「无数据」改为返回 `null`，由行守卫统一跳过。
  - 空调卡片（ClimateCard）、尾门卡片（TailgateCard）在全部数据为空时整卡隐藏。
- 集成 v0.0.0.88 的修复：状态页动力电池容量实时刷新（SOC 0.1% 量纲解码修正 + getter 优先级调整），以及顶栏『关于软件』版权弹窗。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.90-beta-debug.apk` | `ab84a552a8b6ae0af6b519e745ead6737f003953b33400b5a94778bef32fde8e` |

## v0.0.0.88 (beta)

- **修复状态页「动力电池容量」无法实时更新（重要）**：根因是 SOC 解码把有效值当无效值丢弃——车机 `getDispHvBattLvlOfChrg` 返回的是 **0.1% 量纲**原始值（实测 358~466），而解码函数把范围硬卡在 `0~100`，导致全部读数被判无效、`batteryPercent` 全程为 `null`，页面显示「--」且看似不刷新。
  - 解码改为带量纲还原：`raw > 100` 按 `raw / 10` 还原百分比（`358 → 35.8%`），`0~100` 视为直读，无效值（255/254/负数）仍归 `null`。
  - 电池 getter 优先级调整：优先 PA 显示电量 `getPA_VehCharg_DispHvBattLvlOfChrg`，其次直读 `getDispHvBattLvlOfChrg`，最后 `getPA_VehCharg_DisChargSOC`。
  - 实测验证：状态页电量由 `35.8%` 连续刷新至 `58.8%`，`GalaxyLinkStatus` 记录 `runtime=… snapshot=… resolved=…` 持续变化，实时刷新已恢复正常。
- 保留电池 SOC 诊断日志（驱动控制器每 5 秒 `battery diag`、状态页值变化时记录），便于后续排查。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.88-beta-debug.apk` | `44ec6f79a9cb47aa67c66029f3af7b8522cbfd815282d209320bae3d78e8247a` |

## v0.0.0.87 (beta)

- **电池 SOC 实时诊断日志**：为定位「状态页动力电池容量无法实时更新」问题，新增诊断日志——
  - 驱动控制器 `EcarxDriveSignalController`：连接成功时打印实际选中的电池 getter 名与是否 PA 包装；每 5 秒打印 `battery diag: getter=… wrapsPa=… raw=… decoded=… published=…`。
  - 状态页 `GalaxyLinkStatus`：动力电池容量仅在解析值变化时打印 `runtime=… snapshot=… resolved=…`。
- 功能与 v0.0.0.85 一致，本版仅用于采日志定位，建议用于排查后再更新正式版。

## v0.0.0.85 (beta)

- **顶栏『检查更新』改为『关于软件』**：点击后弹出全屏弹窗，内嵌 WebView 加载版权说明页面 `https://www.izhuoe.com/copyright.php`，支持上下滑动浏览，含加载中与加载失败提示；相对资源（如二维码图片）以 `https://www.izhuoe.com/` 为 base 正确解析。
- 启动静默检查更新与更新弹窗逻辑保持不变，仅替换了顶栏手动「检查更新」入口。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.85-beta-debug.apk` | `d6491da6d9e15235d4124a6ffb1a731139127cae51e1cd34758bca095afafd82` |

## v0.0.0.84 (beta)

- **车辆信息页大幅扩充（按 CarSignal 全量信号接入）**：机油、12V 蓄电池电压、油量/油量百分比、续航里程、总里程、保养剩余里程/天数/时长统一改由 car_signal 读取，消除此前「--」缺数据；并新增能源动力、环境（车外温度/PM2.5/雨量）、车身（中控锁/引擎盖/油箱盖/充电口盖/后视镜折叠/方向盘转角/雨刮）、扩展灯光（日行灯/前雾灯/倒车灯/刹车灯/迎宾/离车/欢迎）、座椅（方向盘与后排加热）、制动与驾驶辅助（EPB/ESC/巡航/限速/前碰撞/倒车侧/车道保持）、车身姿态（俯仰/侧倾/卫星数）等卡片。
- **胎压显示单位化**：胎压数值加 `kPa`、胎温加 `℃` 单位。
- **天窗/遮阳帘按百分比显示**：天窗与遮阳帘位置均按行程百分比（0% 全关 ~ 100% 全开）展示，并新增遮阳帘开合检测卡片。
- 灯光区修复：补全完整灯位，统一以 ON/OFF/原始值展示，无数据显示「无数据」。
- 缺失 getter（ABS 警告嵌套对象、车道保持/卫星数改用别名）按车机实际情况优雅降级到「无数据」，不影响其他信号。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.84-beta-debug.apk` | `febc735149ab18909910e6ab1e2ef85039815c1601698914c0230c49d9422181` |

## v0.0.0.82 (beta)

- 车辆信息页「保养信息」相关文案统一：将「清除保养信息」改为「重置保养信息」（提示语、对话框标题/正文、确认按钮、面板副标题同步更新），语义更准确。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.82-beta-debug.apk` | `3abc6581f57a10fd6478e5166079810f5aa2739f74f2d53bed56f67cecb50fa4` |

## v0.0.0.81 (beta)

- **工程结构精简**：将 `core-common` / `core-data` / `core-ui` / `xposed-api-stubs` 四个子模块物理合并进单一 `app` 模块（保留原包名，`import` 零改动），顶层目录显著减少，更易维护。
- **连接层统一**：新增公共 `GalaxyLinkHttpClient`，收敛更新检查与 APK 下载中原先重复的 `HttpURLConnection` 样板（超时 / 重定向 / 大小上限 / 流拷贝 / 日志），消除重复的「连接调用文件」。
- **体积优化**：业务图标 PNG 转无损 WebP（364K→244K），开启 `shrinkResources` 并用 `keep.xml` 保护图标资源；APK 体积 14.34MB→14.21MB。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.81-beta-debug.apk` | `f870345908cde488d4c35518b80d5a5b78e211e968694050416d432814c1889f` |

## v0.0.0.78 (beta)

- 车辆信息页灯光不再用 `--` 掩盖取值，改为**直接显示车机上报的状态值**：`ON(1)` / `OFF(0)` / `DEFAULT(2)` / `ERROR(253)` / `NONE(254)` / `UNKNOWN(255)` / `NO_SIGNAL(-1)`，其余值原样呈现为「值 N」。
- 车窗按 **100% 行程**显示：依据车机 `WinAndRoofAndCurtPosnTyp` 档位换算（`全关=0%`、`全开=100%`，中间每档 4%），并附原始状态值便于对照。
- 车辆信息页所有信号获取**补齐日志**：座舱轮询逐项输出车窗/天窗/空调/转向灯/靠背角度的原始值与解析结果，尾门状态单独打点，便于按项目排查取不到值的问题。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.78-beta-debug.apk` | `f16375a981c4f8a611aa8df6d4886e54a6651b38777521ff1dbb2ec6df7428a5` |

## v0.0.0.77 (beta)

- **在线更新检测改用 HTTPS**：`https://www.izhuoe.com/galaxylink-release-parser.php`（JSON 输出用 `?format=json`）。
- 站点证书链补全：站点只下发叶子证书，中间证书 `YR1` 与根证书 `ISRG Root YR` 均为 2025 年后新增，Android 9（API 28）等旧车机系统 CA 库中没有；现将两者内置为信任锚，使 HTTPS 校验在旧系统上也能通过（根证书交叉锚定到系统内置的 ISRG Root X1，未降低信任强度）。
- 保留 HTTP 作为兜底通道：设备 CA 库确实无法补齐时自动回退，避免在线更新功能整体不可用。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.77-beta-debug.apk` | `92ba4e087559cc26a92522c5165984a6011edb8b4a8a99b6b5e44726753eb6f6` |

## v0.0.0.76 (beta)

- **在线更新检测地址改用官方解析接口**：`http://www.izhuoe.com/galaxylink-release-parser.php`（JSON 输出用 `?format=json`），一次请求即可获取 Gitee / GitHub 聚合后的最新版本。
- 「通用 → 关于与更新」新增「官方解析」更新来源选项，「自动」策略调整为解析接口优先、Gitee / GitHub 依次兜底。
- 修复更新渠道判定：发布仓库 tag 形如 `v0.0.0.75`（4 段、无 `-beta` 后缀），此前会被误判为正式版，导致检查更新始终显示「已是最新」；现按版本号段数推断渠道（4 段为测试版、3 段为正式版）。
- 明文流量按域名精确放行：仅 `izhuoe.com` 允许 HTTP，其余域名仍强制 HTTPS。
- 修复解析接口未返回安装包大小时下载进度停滞、大小校验失效的问题（改用响应头 `Content-Length` 兜底）。

### 安装包校验

| 文件 | SHA-256 |
| --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.76-beta-debug.apk` | `f77de792f9905917c7a0b68a894b6ec3055d1178ad43a173a547fcd1892c6a82` |

## v0.0.0.75 (beta)

- 修复 Android 9（API 28）设备上启动后界面不显示的问题：
  - 移除等高布局中对 `IntrinsicSize.Min` 的使用，改为自定义两遍测量布局，避免 `SubcomposeLayout` 固有测量崩溃；
  - 前台保活服务类型由 `specialUse` 调整为 `dataSync`，并对 `startForeground` 做版本安全与异常保护，消除 API 28 上的启动崩溃。
- 状态页「设备概览」与「实时仪表」改为等高对齐。
- 全部多选一选择控件统一为绿色强调色（与左侧 dock 栏一致）。
- 悬浮球外观：去除自定义像素输入框，大小选项新增 128 px。
- 自动化界面：总开关 / 应用数 / 总延迟 / 返回主页合并进上端统一方框。
- 自动化界面档位与时长文案强制中文（「档」「分钟」）。

### 安装包校验

| 文件 | MD5 | SHA-256 |
| --- | --- | --- |
| `galaxylink-toolkit-nohud-v0.0.0.75-beta-debug.apk` | `f3dac1c7f936ec470b2269ce51240278` | `5575c516f4d92f3f471b0ce500ad7ab9c906935d346b1a70a0a495103b5ffd8a` |
