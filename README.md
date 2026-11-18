# Antigravity Context Ring Addon (`user/` 改装层)

面向 [Anarcadia/chuanban-antigravity-theme-patch](https://github.com/Anarcadia/chuanban-antigravity-theme-patch)（`v5.3.0+`）的无侵入 `user/` 改装包，将输入框底栏原有的纯文本会话指标升级为**可视化上下文容量圆环胶囊 + 分段容量仪表盘卡片**。

## ✨ 核心特性

- **圆环进度胶囊**：输入框底栏首位显示带 SVG 环形进度条的状态胶囊（`11% 57k/512k`），后面紧跟精简的生成速度与输出量（`269 tok/s · Out 770`）。
- **四档健康度配色**：按上下文占用率自动变色——`<50%` 绿（充裕）、`50–74%` 琥珀（适中）、`75–89%` 橙（偏高预警）、`≥90%` 红（即将压缩），完整适配白天三套暖纸预设与官方 `onyx` 黑夜模式。
- **分段容量仪表盘（悬停预览 / 点击固定）**：
  - 顶部 10px 分段进度条区分 **缓存命中（实色）**、**本轮新增（斜纹）** 与 50% / 80% 刻度线；
  - 三栏大字卡片展示 **当前已用**、**模型上限** 与 **剩余可用**；
  - 四栏紧凑指标条展示 **输出速度**、**首字/缓冲等待**、**本步输出** 与 **缓存命中率**，底部附带本轮多步合计、子 Agent 汇总与会话累计；
  - 点击胶囊可固定卡片（支持选中复制文字，点右上角 `×`、空白处或按 `Esc` 取消固定）。
- **零侵入 `core/` 内核**：全部逻辑位于 `custom.css` 与 `custom.js`，通过 `ctx.onCleanup` 管理生命周期，不影响 `core/manifest.json` SHA-256 校验，上游补丁升级时 `user/` 目录不会被覆盖，保存后 2.5 秒热重载生效。

## 📦 安装方式

前置条件：已部署 [chuanban-antigravity-theme-patch](https://github.com/Anarcadia/chuanban-antigravity-theme-patch)（Node.js `>= 22.13.0` 以开启 SQLite 实时指标）。

### macOS / Linux

```bash
# 备份原有 user/（如有）并拉取本改装层
cp -r ~/.gemini/antigravity/theme-patch/user ~/.gemini/antigravity/theme-patch/user.bak.$(date +%s)
git clone https://github.com/huibozi/antigravity-context-ring.git ~/.gemini/antigravity/theme-patch/user.tmp
cp ~/.gemini/antigravity/theme-patch/user.tmp/{config.json,custom.css,custom.js,i18n.override.json} ~/.gemini/antigravity/theme-patch/user/
rm -rf ~/.gemini/antigravity/theme-patch/user.tmp
```

### Windows (PowerShell)

```powershell
$u = "$env:USERPROFILE\.gemini\antigravity\theme-patch\user"
Copy-Item $u "$u.bak" -Recurse -Force
git clone https://github.com/huibozi/antigravity-context-ring.git "$env:TEMP\ag-ctx-ring"
Copy-Item "$env:TEMP\ag-ctx-ring\config.json","$env:TEMP\ag-ctx-ring\custom.css","$env:TEMP\ag-ctx-ring\custom.js","$env:TEMP\ag-ctx-ring\i18n.override.json" $u -Force
Remove-Item "$env:TEMP\ag-ctx-ring" -Recurse -Force
```

守护进程会在 **2.5 秒内自动热重载生效**，无需重启 Antigravity。如需排查或临时绕过 `user/` 层，可运行：

```bash
node ~/.gemini/antigravity/theme-patch/install.mjs verify --safe-mode
```

## 📁 文件说明

- `custom.css`：四档容量语义色变量、SVG 环形进度条样式、悬停/固定仪表盘卡片样式（兼容昼夜双模式）。
- `custom.js`：包装 `window.__ag_render_metrics`，解析守护进程下发的容量与遥测数据并渲染圆环胶囊与详情卡片，支持点击固定与热重载清理。
- `config.json`：默认 `high-contrast` 暖纸预设与全模块开启配置。
- `i18n.override.json`：用户自定义词条覆盖模板。

## 🙏 设计参考

界面与交互设计灵感参考自：
- [elviscjn-cjn/antigravity-context-viewer](https://github.com/elviscjn-cjn/antigravity-context-viewer)
- [DSDS-CMHL/EasyAntigravity](https://github.com/DSDS-CMHL/EasyAntigravity)
- 运行框架基于 [Anarcadia/chuanban-antigravity-theme-patch](https://github.com/Anarcadia/chuanban-antigravity-theme-patch)
