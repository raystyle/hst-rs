# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D50 doctor yolo 诊断面补 bypass 残余阻塞信号

> 用户裁 2026-09-15「开工」（S029 追记候选转正）。依据：S029 追记残余阻塞分类学（官方七页文档加 changelog 取证）；R002 doctor 行。三段：

1. **既有 yolo 检查语义修正（src\doctor.rs）**：项目层读取补 `settings.local.json`（local 优先于 shared，对齐 settings 优先级栈）；项目层 bypass-only（用户层无 bypass）不再判 ok（2.1.257 起项目与 local 层 bypass 被忽略，运行时回退用户层即无 bypass，S029 已档），warn 带 CTA；项目层 bypass 加用户层 bypass 并存保持 ok（detail 注回退到用户层）。
2. **两项新 warn 检查加一项既有检查修正（claude 域）**：`yolo.ask` = permissions.ask 非空（local 与 shared 与 user 三层全查，codex F4 口径对齐；bypass 只跳 allow 层，ask 照弹，S029 第 2 类）；`yolo.readblock` = 任一层 blockReadsOutsideWorkingDirectories 为 true（读沙箱分析闸，不可静态分析命令即使 bypass 也问人，S029 标本类）；既有 yolo conflict warn 补 local 层读取即遮蔽信号（不另立 yolo.mask 检查名，codex 复核裁定 a）。全部 warn 级不 block（部署缺口不计数），detail 带 CTA。
3. **测试与文档**：doctor 单测补三类正负例（mask 双层源、ask 非空、readblock 任一层、项目层 bypass-only 假阳性修正回归）；R002 doctor 行加三检查名与判据、S029 追记挂 D50 落地回指、INDEX doctor.rs 行加 D50；门禁全绿、dogfood 本机实弹、codex 评审对齐（w2:p2，用户令）后推 main（封版时点另裁）。
