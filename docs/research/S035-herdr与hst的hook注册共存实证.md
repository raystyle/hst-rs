# herdr 与 hst 的 hook 注册共存实证

- 日期：2026-09-14
- 关联：S033（四家 hook 与状态栏矩阵）、S025（状态栏命令矩阵）、D28（hook 注册常驻用户级）
- 方法：本机 WSL 现场取证加 herdr 上游源码 clone 对照（herdrdev/herdr，落 `~/herdr`）

## 背景

状态栏显示 `claude-2.1.270:unknown`，怀疑 herdr 的 hook 与 hst 的 hook 在注册面互相冲突。要定性：是覆盖冲突还是另有根因，以及两家工具能否长期共存。

## 关键结论

1. 非冲突：herdr 与 hst 在注册层互相保留外来条目、在运行层通道分离，可稳定共存 [实证： 本机重跑 init 后 `SessionStart` 下两组并存；kimi config.toml 三套 hook 同文件共存]
2. `agent-version:unknown` 的 `unknown` 是状态段兜底值（无 hook 写会话状态时填 unknown），不是模型段故障；模型段来自 stdin JSON 一直正常 [实证： `hst-statusline.ps1` 状态段逻辑加直跑脚本对照]
3. 真因是 WSL 侧 `hst init` 未完成注册阶段：shim 已落盘（`~/.hst/hooks/` 四件）而四家注册全缺，`hst doctor` 四家 `hooks.form` 全 warn。herdr 的安装不是原因 [实证： 时间线与 kimi 侧 hst 条目在 herdr 安装后仍存活]
4. 跨侧配置搬家会带来异侧形态残留：Windows 形态注册（`powershell.exe` 加 `C:/` 路径）搬进 WSL 后 doctor 报 `shim-dead`；hst init 的陈旧形态覆盖设计会重写为本侧形态，外来条目（herdr 的）不动 [实证： kimi config.toml 重跑 init 后形态翻转]

## 现状或实测

### 修复前现场

- `~/.claude/settings.json` 的 hooks 段只有 herdr 一条 SessionStart（matcher `*`），`~/.hst/state/` 目录不存在 [实证]
- `hst doctor`：claude 与 codex 与 grok 报 `no hst hooks`，kimi 报 `shim-dead`；四家 statusline 检查全 ok [实证]
- 时间线：`~/.hst/hooks/` shim 落盘 10:17，herdr 集成与 settings.json 落盘 10:32；hst init 流程是先写 shim 再四家注册（`deploy_user_hooks_with`），shim 在而注册缺说明 init 中断于注册之前 [实证]

### herdr 安装器取证

> 源码对照 `src/integration/claude_settings.rs`（上游仓 clone 于 `~/herdr`）。

- CST 保格式编辑（jsonc_parser cst 层），非整文件重排 [实证]
- 只删自家命令：`is_matching_command_hook` 按 herdr-agent-state 脚本路径变体匹配，外来条目保留有专门回归测试（`install_removes_only_owned_commands_from_shared_hook_groups`、`install_migrates_wildcard_session_start_and_preserves_user_hook`）[实证]
- 装机版本与上游源码有代差：本机集成 v9，SessionStart matcher 为 `*`；上游 v10 canonical 已收窄为 `^(startup|resume|clear|compact|fork)$`，并含 wildcard 迁移时保留用户条目的测试 [实证]

### hst 侧对照

> 源码 `src\deploy.rs`。

- `deploy_claude_user` 逐事件 `merge_hook_event` 幂等合并，ours 判定认全路径解释器头，外来定义保留（F4 收窄原则同 codex 侧）[实证]
- kimi 侧注册为逐条 `[[hooks]]`，陈旧形态（老命令形态、异侧路径）按「不等于本次要写的 shim 命令」覆盖 [实证]

### 共存落地验证

- 重跑 `hst init`：`SessionStart` 下 herdr 一组加 hst 一组并存，四家 `hooks.form` 与 statusline 检查全 ok [实证]
- 注册生效当帧起本会话 PreToolUse 即写状态（`~/.hst/state/claude-<session>.json`），直跑状态栏脚本输出 `claude-2.1.270:working` [实证]
- 运行层通道分离：herdr hook 无 `HERDR_ENV` 或 `HERDR_SOCKET_PATH` 或 `HERDR_PANE_ID` 即 exit 0，只向 herdr daemon 上报；hst hook 只写 `~/.hst/state/`，互不读写对方数据 [实证]

## 踩坑沉淀

- 现象：状态栏 `agent-version:unknown`，且机器上先后装过两个 hook 类工具，直觉指向覆盖冲突。
- 根因：hst 注册缺失（init 中断），与 herdr 无关；直觉归因外部工具前少跑了一次 `hst doctor`。
- 正确处理：先 `hst doctor` 看 `hooks.form`（该检查第一屏即给出 `hst init deploys` 提示），再决定是否怀疑外部工具；`unknown` 兜底在状态段，排查入口是「状态通道是否有人写」而非「状态栏是否坏了」。
- 跨侧搬家：Windows 与 WSL 两份家目录配置互搬后，异侧形态注册会 shim-dead；hst 侧重跑 init 自愈，herdr 侧异侧条目需在其本侧 uninstall 清理。

## 待办

- herdr 升级到 v10 后复核：重装集成应自动把自家 `*` matcher 条目迁移为收窄形态并保留 hst 条目 [假设： 依 v10 源码测试推断，装机后待验]
- kimi `config.toml` 顶部 Windows 形态 herdr 条目在 WSL 侧经 interop 双报或空转，建议 Windows 侧 herdr uninstall 清理 [待办]
- 本次 init 中断于「shim 落盘后、注册写入前」的具体报错未复现，无日志可考；若再发可补记 [待办]
