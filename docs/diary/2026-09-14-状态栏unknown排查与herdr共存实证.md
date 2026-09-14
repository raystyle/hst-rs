# 2026-09-14：状态栏unknown排查与herdr共存实证

> WSL 侧状态栏显示 `claude-2.1.270:unknown`，疑 herdr 的 hook 与 hst 冲突；排查定性为非冲突（WSL 侧 hst 注册缺失），重跑 init 自愈并落 S035。本篇记流水与自省。

## 流水

1. **排查定性（非冲突）**：`hst doctor` 一屏定位，四家 `hooks.form` 全 warn（claude 与 codex 与 grok 报 no hst hooks、kimi 报 shim-dead）；`~/.claude/settings.json` hooks 段只剩 herdr 一条、`~/.hst/state/` 不存在。`unknown` 实为状态段兜底值（无 hook 写会话状态时填），非模型段故障。时间线取证：hst 的 shim 落盘 10:17、herdr 装机 10:32，init 流程是先写 shim 再四家注册，shim 在而注册缺说明 init 中断于注册之前，与 herdr 无关。
2. **herdr 双向取证**：clone 上游 herdrdev/herdr 落 `~/herdr`；安装器源码实证 CST 保格式编辑、只删自家命令、外来条目保留（有专门回归测试）；装机集成 v9（SessionStart matcher `*`）落后上游 v10（收窄为 `^(startup|resume|clear|compact|fork)$` 且带 wildcard 迁移保留测试）。kimi config.toml 三套 hook 同文件共存（Windows 形态 herdr、Windows 形态 hst、Unix 形态 herdr）是「herdr 不删外来条目」的活证。
3. **修复与验证**：四家注册文件备份 `/tmp/hst-herdr-diag/` 后重跑 `hst init`：SessionStart 下 herdr 与 hst 两组并存、四家 hooks.form 与 statusline 检查全 ok、注册生效当帧起 PreToolUse 实时写状态、直跑状态栏脚本输出 `claude-2.1.270:working`。仓内 SKILL.md regen 仅换行符差异，还原保持仓库干净。
4. **S035 落档（9f1ce08）**：研究文档六态标注加 INDEX 研究节登记；四个带括号小节标题被 md-heading-scan 抓出即改（括号信息降为节首引用行）；验证四件套全绿（断链 0、标题 0、字符通过、rumdl 通过）。
5. **ohmycloud 跨仓回执**：来函 env 桶 oma/stable 与 oma/dev 段对象已删（旧 oma self update 通道断流属预期）；全仓 grep 实证 hst 镜像只走 `env.ohmygh.com/hst/dev`（update.rs 4 处，stable 通道直连 GitHub 不落镜像）、安装链 D20 后无下载面、无任何 oma 或 ome 段引用（OMA_* 兼容读 D45 已删，`oma` 字样仅存本地 `~/.oma` 目录改名迁移逻辑）；herdr agent prompt 回执 ohmycloud 会话 w4:p1（agent_prompted 确认）：段删除对 hst 零影响。

## 自省

- 直觉归因外部工具冲突前，自家产品就有第一屏诊断面（doctor 的 hooks.form 检查自带 `hst init deploys` 提示）；先跑 doctor 再怀疑外部工具，能省一轮源码取证。
- 「两工具先后动过同一配置」不等于「互相冲突」：注册层是否覆盖要看双方安装器的合并语义（本案双向保留外来条目，双方源码都有测试佐证）；时间线证据（shim 与注册的落盘顺序对照 init 流程）比直觉可靠。
- 跨侧配置搬家残留是第三种病因：Windows 形态注册搬进 WSL 后报 shim-dead，既非冲突也非覆盖，重跑 init 按陈旧形态覆盖设计自愈；异侧外来条目（herdr 的 Windows 形态）须其本侧 uninstall，不越权代删。
