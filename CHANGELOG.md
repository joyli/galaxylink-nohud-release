# 更新日志

本文件记录「星越L银河助手 · 无 HUD 版」的公开版本变更。

> **产物策略（v0.0.0.88 起生效）**
>
> - **双端 Releases 页面：每个版本永久保留，不删除** —— GitHub / Gitee 已发布的版本按版本迭代累加，历史版本可随时下载。
> - **本地仓库 `releases/` 目录：只保留最新版本安装包** —— 旧版安装包不在仓库内累积，避免体积膨胀。
> - 本文件的变更记录永久保留。
>
> 当前最新：**v0.0.0.90** → `releases/v0.0.0.90/galaxylink-toolkit-nohud-v0.0.0.90-beta-debug.apk`

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
