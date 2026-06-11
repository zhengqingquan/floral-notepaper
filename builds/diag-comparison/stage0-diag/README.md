# Stage 0 — 仅诊断日志（行为 = upstream/main）

## 目的

在 **未引入任何便签池修复** 的前提下，复现并记录 Ctrl+空格连按时的 webview 计数与事件序列，作为后续 stage 对比基线。

## 构建

```powershell
npm run tauri build
```

产物复制为：

```
builds/diag-comparison/stage0-diag/floral-notepaper-stage0-diag.exe
```

## 诊断模式

- `init_notepad_pool_diagnostic_logging("stage0_diag")`
- 日志文件：`%LOCALAPPDATA%\floral-notepaper\notepad-pool-diag.log`
- Windows 下同时输出到控制台（`ensure_console`）

## 验证步骤

1. 启动 exe，等待约 2s（prewarm 调度完成）
2. 快速连按 Ctrl+空格 10～20 次
3. 关闭所有便签
4. 对比日志中 `standby` / `visible` / `hidden` / `webviews` 是否出现 **hidden > standby**（泄漏征象）

## 预期（基线行为）

- `prewarm_put_fail` 可能出现（池满时 put 被忽略，webview 仍保留 → 泄漏）
- 无 `creation_lock` / `dispatch_coalesce` / `deferred_drain` 等后续 stage 事件
- 可能出现卡死或无响应（用于与 stage3 对比）
