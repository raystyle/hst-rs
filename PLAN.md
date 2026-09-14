# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D49 技能名翻 hst 加旧名兼容窗

> 用户裁 2026-09-14「走」立项（第 1 轮带兼容窗），同日第 2 轮令「老的 ohmyagents 应该删除啊」取消兼容窗：旧牌直接删除加幂等退役。依据：PRD D49 第 2 轮；ours 识别沿 write_skill marker 家族。三段：

1. **skillgen 单名渲染（src\skillgen.rs）**：`render_skill` 唯一名 hst（第 2 轮删 legacy 渲染与常量）。
2. **单写加退役（src\main.rs 加 src\deploy.rs）**：用户级 `hst skill --write` 只写 `~/.claude/skills/hst/`，旧牌目录幂等退役（生成签名识别，打 `skill.retired=`）；项目级 `deploy_skills` 四处（.agents/.claude/.grok/.kimi-code）各写 `skills/hst/`，旧牌 ours 目录（marker 家族识别）退役打 `(retired)`，用户手改或他源不动；仓自有 `.agents/skills/ohmyagents` 跟踪件删除。
3. **测试与文档**：skillgen 断言唯一名；deploy 集成断言四处 hst 在位加旧牌 ours 目录退役加用户手改保留；门禁全绿加 dogfood（退役实弹）；文档四处同步（AGENTS 意图路由行、R002 skill 行、INDEX 三行、main.rs 命令注释）随第 2 轮口径重写；codex 评审对齐后推 main（封版时点另裁）。
