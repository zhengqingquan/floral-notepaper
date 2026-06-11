# Stage 2 — 死锁修复 + 诊断日志

## 相对 Stage 1 新增

- `try_acquire_creation`：`open_new` 不再阻塞等待 prewarm 持锁（避免主线程自死锁）
- `dispatch_quick_notepad_open`：快捷键/托盘连按合并为单次主线程派发
- prewarm / creation 进行中时 skip 并 `note_quick_open_pending`
- `deferred_drain`：锁忙时 50ms 后补开（链式最多 4 次）
- prewarm 结束后 `run_deferred_quick_notepad_open_after_prewarm`
- `show_and_activate_notepad`：Rust 侧仅 show + emit，焦点交给前端
- 可见便签 >2 时 `pool_drain` 关闭待机 WebView

## 尚未包含（Stage 3）

- `400ms` WebView 创建冷却（`NOTEPAD_WEBVIEW_CREATE_COOLDOWN_MS`）
- `mark_webview_created` / `creation_cooldown_remaining`
- 冷却期派发层 skip 与 prewarm 延后重调度

## 构建

```powershell
npm run tauri build
```

产物：`builds/diag-comparison/stage2-deadlock-fix-diag/floral-notepaper-stage2-deadlock-fix-diag.exe`

## 日志

- 模式：`stage2_deadlock_fix_diag`
- 路径：`%LOCALAPPDATA%\floral-notepaper\notepad-pool-diag.log`

## 对比 Stage 1 预期

- 不再停在 `prewarm_build_begin` 无 end（死锁）
- 出现 `dispatch_coalesced`、`creation_lock_busy`、`deferred_drain_wakeup`
- 快速连按仍可能挂死（Stage 3 冷却修复）
