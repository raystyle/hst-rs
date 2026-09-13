# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D46 状态栏 agent 版本段

> v1.1.4 方向；发版链与评审闸门见第五段。

> 依据：PRD D46（用户裁 2026-09-13 加追问链三裁、第 2 轮实弹两裁：连字符形与 codex 面去 context-remaining）；S025 机读标记契约、S034 D40 落地追记（codex 内置项面）、D44 两行终态（DEFAULT_SEGMENTS2）。五段：

1. **版本段实现（src\statusline.rs）**：SEG_OMA 段块加版本获取两级：payload `version` 字段优先（claude/kimi/grok stdin 契约都带，三家二进制实证见 S034 追记）；否则 mtime 键控 probe：每帧定位二进制取 `LastWriteTimeUtc`（零子进程），与缓存比对，mtime 变了或无缓存才重探 `& <bin> --version`（两步取值：stdout 优先、正则未中再合流取 stderr，与 read_version 同口径；正则取 `数字.数字` 起头版本串，payload 值同归一化），探测失败写空值加 `probed_at`（5 分钟静默窗内不重试，防每帧 spawn；无缓存可写不探）。缓存按 agent 单文件 `~/.hst/cache/agent-version-<agent>.json`（键 = 终目标路径/mtime/size 三元组，ticks 整数记；软链按链接所在目录解析；7 天兜底重探；临时件加 [IO.File]::Move 原子落盘），`HST_VER_CACHE_DIR` env 覆盖目录（verify 与测试隔离通道，对齐 HST_STATE_FILE 先例）。版本并入 `{agent}` 值（第 2 轮裁连字符形 `claude-2.1.268`），模板与占位符零变化、用户自定义模板兼容；**$agent 变量本体不动**（state 文件定位仍吃纯名），另立 `$agentDisp` 渲染。探不到版本回落旧形 `claude:working`。
2. **机读标记与 codex 面（src\verify.rs 加 statusline.rs）**：`statusline_marker_ok` 判据改两形兼容（`<agent>:` 直连旧形、`<agent>-<version>:` 新形，版本 token 数字起头且含点）；codex 缺省内置项集 `CODEX_STATUS_LINE_ITEMS` 加 `codex-version`（源码取证：`StatusLineItem::CodexVersion`，strum kebab_case，run-state 首位锚后插）并裁去 `context-remaining`（用户令只留 `Context N% used`）回十二项。
3. **测试与门禁**：单测（marker 两形判据正负例、codex 十二项烘焙、PS1 含版本两级逻辑与 HST_VER_CACHE_DIR）；集成（mock stdin 带 version 字段出新形标记、无 version 且二进制不在 PATH 回落旧形、HST_VER_CACHE_DIR 隔离下 probe 路径真跑一次后缓存命中、stderr-only 版本回落正例）；全量测试加 fmt/clippy 加 rumdl 加 .tools 三扫描。
4. **文档同步**：R002 状态栏行（版本段口径、缓存与 env、marker 形变、codex 十二项）与 verify 行（判据两形）；S025 机读标记契约 D46 修订（规范语法与容错匹配口径，herdr 消费面知会随回执）；S034 D46 追记（codex-version 实证、三家 payload 实证、缓存坑）；INDEX statusline 与 verify 行、CHANGELOG v1.1.4 里程碑。
5. **评审与发版**：herdr 右侧 codex 多轮（设计轮 F1 至 F8、diff 轮 F1 至 F6 加 G1、终验轮 G2 至 G4 全收）达成一致后 tag v1.1.4（用户令「要让右侧 codex review」）；CI 绿、镜像 hst/stable 滚动 digest 三方对账、本机换装重跑 `hst statusline` 实弹；herdr 回执 ohmycloud（含 S025 连字符形标记知会，herdr 消费面同步）；wsl 总台 verify G8 面复验随新版（对端动作，回执后关账）；TODO 收口加 diary。
