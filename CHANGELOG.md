# 更新日志

本文件记录「星越L银河助手 · 无 HUD 版」的公开版本变更。

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
