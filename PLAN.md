# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D49 技能名翻 hst 加旧名兼容窗

> 用户裁 2026-09-14「走」（D14/D22/D45 旧牌保留裁定的翻案）。依据：PRD D49；D29 至 D45 兼容窗模式（旧名双写一个 minor 窗口后清扫）。三段：

1. **skillgen 双名渲染（src\skillgen.rs）**：`render_skill_as(name, cta)` 单体渲染，canonical `render_skill`（name = hst）加 `render_skill_legacy`（name = ohmyagents、description 首带「旧牌兼容窗至 1.2.0，建议改用 hst 技能」CTA）；常量 SKILL_NAME / SKILL_NAME_LEGACY / SKILL_LEGACY_CTA 供 main 与窗口期引用。
2. **双写落盘（src\main.rs 加 src\deploy.rs）**：用户级 `hst skill --write` 写 `~/.claude/skills/hst/` 加 `~/.claude/skills/ohmyagents/`（marker `skill.wrote` 两行，兼容行带 `(compat)` 尾注）；项目级 `deploy_skills` 四处（.agents/.claude/.grok/.kimi-code）各写 canonical 加兼容双份，`write_skill` 体由调用方传入（判等与覆写各对各的体，用户手改跳过语义不动）；`deploy_kimi_project` 布局面随 canonical；仓自有 `.agents/skills/ohmyagents` git mv 为 `hst`。
3. **测试与文档**：skillgen 断言翻 hst 加 legacy 双断言（name 加 CTA）；deploy 集成断言四处 canonical 在位加兼容体 CTA；门禁全绿加 dogfood（用户级与项目级双技能落位实弹）；文档四处同步（AGENTS 意图路由行、R002 skill 行、INDEX skillgen 行、main.rs 命令注释）；TODO 队列登记 1.2.0 摘双写加清扫；codex 评审对齐后推 main（封版时点另裁）。
