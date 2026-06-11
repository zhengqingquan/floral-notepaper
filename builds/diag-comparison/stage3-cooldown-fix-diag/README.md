# Stage 3 — 冷却修复（v5 完整逻辑）+ 诊断日志

## 相对 Stage 2 新增

- `NOTEPAD_WEBVIEW_CREATE_COOLDOWN_MS = 400`：连续 WebView build 最小间隔
- `last_webview_created_at` + `mark_webview_created`：prewarm / open_new 完成后记录时间
- `creation_cooldown` / `prewarm_defer_cooldown`：冷却期内 defer 打开与 replenish
- `should_defer_quick_open_for_cooldown`：池空且冷却中时派发层 skip + 延后补开
- `schedule_deferred_notepad_drain_from_pool` 使用剩余冷却时间（默认 400ms）

## 预期（与 v5 诊断版一致）

- 快速 Ctrl+空格连按不再未响应/死机
- 日志可见 `creation_cooldown`、`deferred_drain_wakeup`、`main_open_deferred`
- 第二个 `open_new_build_begin` 与第一个间隔 ≥400ms

## 构建

```powershell
npm run tauri build
```

产物：`builds/diag-comparison/stage3-cooldown-fix-diag/floral-notepaper-stage3-cooldown-fix-diag.exe`

## 日志

- 模式：`stage3_cooldown_fix_diag`
- 路径：`%LOCALAPPDATA%\floral-notepaper\notepad-pool-diag.log`

## 下一步

四版 exe 对比验证通过后，Commit 5 删除全部诊断代码。
