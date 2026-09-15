# S029-bypassPermissions会话层失效与命令面注入

> 2026-09-02。用户报修：oma 拉起的 claude 会话 `/permissions` 非 bypass、Allow 规则堆积即审批实锤；配置层 `oma doctor` 全绿。用户定调：bypassPermissions 是 **agent 的命令与配置**：oma init 写的配置对**已存在会话不实时生效**（已活会话不重读 settings），命令面注入才是可靠通道。

## 现象取证

- [实证: 本机 transcript 与 settings] oma pane 会话审批照弹：项目 `.claude/settings.local.json` 堆积 **119 条 Allow**（mtime 停在 13:46 后不再长：旧会话堆积，非当日在涨）；用户手拉的会话（带 flag 起）transcript 77 条 `permissionMode:"bypassPermissions"` 全程 bypass。
- [实证: 无头探针] `claude -p` 令跑 `echo probe-$RANDOM` 回读：带 `--dangerously-skip-permissions` 与不带（靠 user 层 defaultMode）**都真执行**（随机值回读命中）：两种通道在无头下都活。
- [实证: 排除假线索] 曾在 transcript 抓到 `"bypassPermissions is not available when ANTHROPIC_BASE_URL is set"`：时间戳核对全是**自己 grep 的回显**（transcript 自指陷阱）；官方限制清单（见下）无 BASE_URL 条目，排除网关假设。

## 机制

> claude-code-guide 代理取证，官方文档背书（链接在条目内）。

- **模式取值顺序**：`--permission-mode` / `--dangerously-skip-permissions` flag > settings `permissions.defaultMode` > 内置默认（docs permission-modes "Which mode a session starts in"）。
- **settings 栈**：managed > `--settings` > 项目 `settings.local.json` > 项目 `settings.json` > 用户 `~/.claude/settings.json`；`defaultMode` 是标量按栈取最高层，`allow/deny` 列表跨层合并不覆盖（docs settings "Settings precedence"）。
- **v2.1.257 起：项目层与 local 层的 `defaultMode:"bypassPermissions"` 被忽略**（与 `auto` 同等待遇），changelog 原文要求写到 user/managed 或传 `--permission-mode`：oma pane 会话正是「项目层 bypass + 无 flag」形态，命中此条。
- **flag 与 defaultMode 等效且 per-session 优先**；唯一反制是 managed `permissions.disableBypassPermissionsMode:"disable"`（会直接拒绝 flag）与 `--restricted`。
- **Allow 规则堆积 = 从未进过 bypass 的反证**（bypass 下 allow 规则无效、审批不弹）。
- 交互式首启有一次性 dangerous-mode 接受对话框（`skipDangerousModePermissionPrompt: true` 只跳框不是启用前置）；`--resume` 沿用保存时的模式。

## oma 落点

- [实证: 已落地] `plan_agents`（src\orch.rs）claude 路 argv 固定追加 `--dangerously-skip-permissions`：命令面强制通道，免疫 settings 层级与 2.1.257 项目层忽略；别名 argv 追加其后不冲突；`oma respawn` 走同一 plan 重建自动吃到。
- 已存在会话不吃新 argv（用户定调：init 配置面不实时）：**重开该路即生效**：`oma respawn claude`。
- 测试：`claude_argv_carries_bypass_flag_others_do_not`（其它家不沾 flag、stub 路不受影响）；`profile_alias_injects_env_and_argv` 契约更新为 `[bin, flag]`。

## 遗留

- 新机器首启仍会弹一次 dangerous-mode 接受框（user 层 skipPrompt 需首启后才有）：settle 白名单可覆盖。
- codex/grok/kimi 的 yolo 走各自配置面（S007），不进 argv。

## 追记：bypass 后残余阻塞分类学

> 2026-09-15。用户问：yolo 设 bypassPermissions 后为何仍不停阻塞交互；附实弹标本（browse-rs 会话）。方法：claude-code-guide 代理对官方文档（permission-modes、permissions、settings、settings-reference、mcp、sessions、sandboxing）与 CHANGELOG 全量扫描取证。

### 实弹标本

- [实证： browse-rs 会话截屏] 内联 `python -c` 被「Do you want to proceed?」拦截，文案点名机制：`permissions.blockReadsOutsideWorkingDirectories` 读块在场时，shell 解析器无法静态分析的命令（内联代码即典型）问人。该类阻塞属**沙箱读限制分析闸**，非权限规则层。

### 残余阻塞类清单

> bypass 下仍弹，官方口径逐条标源。

1. **读沙箱读块**：`blockReadsOutsideWorkingDirectories` 在场时，出工作目录读与未沙箱化重试「即使在 auto 与 bypassPermissions 模式下也会提示」（需 2.1.257+；2.1.271 修 `cd` 加 git 链与子 shell 漏拦 bug）[实证： 官方 permission-modes「Actions no mode auto-approves」节加标本]。
2. **显式 ask 规则**：bypass 下仍提示（最常见的惊讶源）；deny 规则全模式拦截；项目层 ask/deny 按优先级栈盖过用户层 bypass [实证： 同上]。
3. **灾难删除安全网**：关键路径 `rm` / `rmdir`（文件系统根、根下顶层、家目录、当前与父目录、`"$VAR"/*` 形含 `$()` 与反引号内）bypass 下也须批准，任何 allow 规则与 hook 都不能代批（2.1.126 立、2.1.208 扩形）[实证： 同上加 changelog]。
4. **folder trust 对话框**：完全不受权限模式管（信任键 `~/.claude.json` 的 `projects.<path>.hasTrustDialogAccepted`，按 git 仓库根键控）；未信任时项目 allow 规则与 `additionalDirectories` 被扣、`.mcp.json` 审批挂起（2.1.196 起克隆仓不能自批自己的服务器）；`/cd` 到未信任目录重弹 [实证: 官方 permissions「workspace trust」节]。
5. **MCP 面**：`.mcp.json` 审批框要 bypass 加 `skipDangerousModePermissionPrompt` **双在场**才跳（文档明示 bypass 单独不够）；connector 组织 ask 控制 bypass 仍弹且无记住项；MCP `requiresUserInteraction` 工具每模式都弹 [实证： 官方 mcp 文档]。
6. **resume 不还原模式**：存了 bypass 的会话 resume 后回手动模式，须传 `--permission-mode` 或 `--dangerously-skip-permissions`；会话选择器与 `/resume` 同样不还原 [实证： 官方 sessions「Permission mode on resume」节]。**本文件前文「`--resume` 沿用保存时的模式」系旧记，据此证伪更正**。
7. **跨会话消息与托管设置安全框**：入站跨会话消息保留与 `isolatePeerMachines` 批准（2.1.224）、托管/服务器托管 settings 的 hook 与 shell 命令等安全批准框（2.1.232 加 2.1.260 加 2.1.269），独立于权限模式 [实证： changelog]。
8. **hook 不被 bypass 跳过**：PreToolUse hook 每模式照跑（收 `permission_mode` 可分支）；其 ask 决策在 bypass 下是否强制交互未见于文档 [记忆： 待复核]。

### 项目层覆盖与静默降级

- 优先级栈项目层高于用户层：项目 `defaultMode:"auto"` 会静默盖掉用户层 bypass，全线提示回潮（auto 模式自身有首读外目录、重复阻塞回退等专属提示类）[实证： settings 优先级图]。
- 2.1.257 起项目层与 local 层 `bypassPermissions` 与 `auto` 被忽略（本文件前文已记，官方 changelog 原文确认）。
- IDE 面：VS Code 不读项目层启动模式且需扩展开关放行 bypass 否则以手动模式起；Desktop 每文件夹记模式可盖配置 [实证： permission-modes「Switch permission modes」三分页]。

### flag 对 defaultMode 的差集

`--dangerously-skip-permissions` 与用户层 `defaultMode` 等效选模式，另加：启动参数免疫 2.1.257 项目层忽略、resume 时覆盖还原、`-p` 下残余提示类**转拒绝而非问**、`claude agents` 下显式免责（2.1.196）、2.1.121 加 2.1.126 起覆盖受保护路径写（`.claude` / `.git` / `.vscode` / shell 配置，灾难删除网除外）[实证： permission-modes 加 changelog]。

### changelog 扫描结论

2.1.2xx 无未结的「bypass 仍提示」回归条目；相关集 = 2.1.271、2.1.257、2.1.248（`--restricted` 拒 bypass）、2.1.223（代理 bypass 须守组织禁用）、2.1.208、2.1.196；早期 = 2.1.157 加 2.1.97（沙箱网络提示自 2.1.97 起 bypass/auto 自动批）、2.1.110、2.0.71 [实证： anthropics/claude-code CHANGELOG main]。

### 宿主实弹案例与 D52 修复

- [实证： ohmycloud 2026-09-15 lan-win] yolo.readblock warn 在真机定位到实案（读块键导致的残余阻塞被 doctor 直接点名，正面价值已验）。
- 宿主「init 洗键」两报归因演进（三次修正，教训在案）：第 1 报疑 init 覆盖，我方复现证伪（无 BOM 合并正确、BOM 下响亮报错零改写）；第 2 报（干净无 BOM 文件仍洗成 permissions={skip:true} 单键）先误归因为 BOM 假报，反例证伪后以**逐字节指纹**定位：受损形态 = `hst init --yolo=off` 在宿主原样文件上的精确产出（retire 摘 defaultMode 加 D52 落的 blockReads==false、不动 permissions 内异位 skip、codex 面只摘不写，三症状同源）[实证： 本机沙箱复现逐键一致]；off 分支不打印 init.scope=full，与宿主引文矛盾，推定包装脚本串联 off 清理趟，待宿主命令行取证结案。D52 修复面（读侧 BOM 容忍 + yolo full 落 blockReads=false + doctor yolo.parse）不受影响照常成立。

### hst 落点评估与待办

- hst yolo full 三键（bypass 用户层加 skipDangerousModePermissionPrompt 加 enableAllProjectMcpServers）已盖：一次性 dangerous 框、MCP 审批框（双在场条件）、`.mcp.json` 全批；用户层 bypass 不受 2.1.257 项目层忽略影响 [实证： yolo.rs 写入面对照本清单]。
- 残余**不可消除类**（设计如此）：灾难删除安全网、connector 组织 ask、requiresUserInteraction、folder trust（doctor 已有 trust.project 检查面覆盖）。
- 残余**可诊断类**（doctor yolo 面候选，候选 D50）：项目层 `ask` 规则或 `defaultMode` 在场（静默盖用户层）、`blockReadsOutsideWorkingDirectories` 在场（读块分析闸）、resume 后模式回退（操作面提示：重开带 flag）。**已立项 D50 并交付（2026-09-15）**：yolo 检查补 local 层读取加项目层 bypass-only 假阳性修正（2.1.257 忽略语义），新检查 `yolo.ask` 与 `yolo.readblock`；resume 回退属操作面知识不进检查面（留本档）。
- S029 本体修正一处：见上第 6 条（resume 旧记证伪）。

主要来源：code.claude.com/docs/en/ 的 permission-modes、permissions、settings、settings-reference、mcp、sessions、sandboxing 七页加 anthropics/claude-code CHANGELOG。
