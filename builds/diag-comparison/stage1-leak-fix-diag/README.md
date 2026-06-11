# Stage 1 — 泄漏修复 + 诊断日志

## 相对 Stage 0 新增

- `creation_lock` 串行化 prewarm / open_new WebView 创建
- `replenish_pending` 合并 replenish 调度（单次 800ms 启动预热）
- prewarm `put` 失败时关闭孤儿 WebView（`prewarm_leak_closed`）
- build 后可见数变化时中止并关闭（`prewarm_abort`）
- `orphan_trim` 回收后修剪不在池中的隐藏便签
- `open_in_progress` + `notepadOpenBusy`（Rust + 前端防连点）
- `should_prewarm_notepad` 可见便签过多时跳过后台 prewarm

## 尚未包含（Stage 2/3）

- `try_lock` / `dispatch_quick_notepad_open` 合并派发
- `400ms` WebView 创建冷却与 deferred drain
- `show_and_activate_notepad`（Rust 侧去 `set_focus`）

## 构建

```powershell
npm run tauri build
```

产物：`builds/diag-comparison/stage1-leak-fix-diag/floral-notepaper-stage1-leak-fix-diag.exe`

## 日志

- 模式：`stage1_leak_fix_diag`
- 路径：`%LOCALAPPDATA%\floral-notepaper\notepad-pool-diag.log`

## 对比 Stage 0 预期

- `webviews ≈ standby + visible`（泄漏应明显减轻）
- 仍可能在快速连按时出现死锁/挂死（Stage 2/3 修复）
