# TODO：当前目标任务进度清单

> 角色：**当前目标的任务进度清单**。当前目标完成后，过程与经验回填到 `docs\proven\` 对应方案，并起新清单；当天做的事只记 `docs\diary\`；需求的取舍与状态见 `PRD.md`。

## 当前目标

D47 Windows 构建切 gnu 交叉编译（2026-09-14 立项，摆脱 VC）：

- [x] CI 交叉岗（.github\workflows\dev-release.yml：windows-latest msvc 岗换 ubuntu-latest gnu 交叉岗；apt mingw-w64 条件步、Test 交叉岗跳过注明 linux/mac 双岗覆盖、Package 的 exe 后缀改判 triple）
- [x] self update 切 gnu（src\update.rs：host_asset_name windows 臂改 hst-{arch}-pc-windows-gnu.zip；host_keywords 改 windows-gnu 优先加 msvc 回落加通用 windows 词保底，注释记 2026-09-14 裁定）
- [x] 测试三面（host_asset_name 的 windows 断言改 gnu 名；pick_asset 资产列表换 gnu 断言选 gnu，补 msvc-only 回落断言）
- [x] 本机交叉复验（mingw 一次过，PE32+ console x86-64，12.13MB 与 ohmycloud 实证同量级）
- [ ] 门禁（cargo test --locked 全绿、md 四件套）
- [x] 四原语与文档同步（PRD D47、GOAL 起点锚点进程、PLAN、本清单；随 codex 评审 F1/F2 补 S028 D47 追记与 INDEX update.rs 行标签）
- [x] 提交推 main、CI 绿与 dev release gnu 资产名回报（6995cb3 加 11fffa5；run 34840283806 六岗全绿，dev 六资产 windows 位 hst-x86_64-pc-windows-gnu.zip，镜像 hst/dev 到货；未动版本号未推 tag，herdr 回执 ohmycloud 已发）

D46 状态栏 agent 版本段（2026-09-13 立项，v1.1.4 方向）：

- [x] 版本段实现（src\statusline.rs：SEG_OMA 两级版本获取、mtime 键控 probe 加 5 分钟失败静默窗、`~/.hst/cache/agent-version-<agent>.json` 单文件缓存、HST_VER_CACHE_DIR 覆盖、版本并入 `{agent}` 值模板零变化、$agent 本体不动）
- [x] 机读标记与 codex 面（src\verify.rs marker 判据两形兼容连字符形；CODEX_STATUS_LINE_ITEMS 加 codex-version 并裁去 context-remaining 回十二项）
- [x] 测试与门禁（174 单测加 28 集成绿；fmt/clippy 净增零；rumdl 加 .tools 三扫描全绿）
- [x] 文档同步（R002 状态栏与 verify 与 hook 行、S025 标记契约 D46 修订、S034 D46 追记、R001 cache 登记、INDEX、CHANGELOG v1.1.4）
- [x] herdr 右侧 codex 多轮 review 达成一致零异议放行（设计轮 F1 至 F8、diff 轮 F1 至 F6、G1、终验轮 G2 至 G4，四提交全收；用户令）
- [x] 发版 v1.1.4（tag 28d1e41、CI 绿、六资产 digest 三方对账全等 win c3e14ad6、镜像 hst/stable 到货、本机换装重部署 doctor 绿、herdr 回执 ohmycloud 已发含 S025 连字符形与 G3 正则口径知会）
- [ ] wsl 总台 verify G8 面复验随新版（对端动作，回执后关账）
- [x] 收口（TODO 清账、GOAL 历史与锚点清、PRD 已交付、diary 追记）

D31 至 D45 前目标（2026-09-13 ohmycloud 协调批与状态栏 HUD 迭代五连发，v1.0.0 至 v1.1.3；G2 补钉一行除外，见队列）：清单见 git 历史。

- [ ] G2 补钉：kimi / grok 运行时退单排的 pwsh 闸门断言（codex D40 评审记下批，不阻塞；D42 三行测试已顺带钉 kimi 并一行，剩 grok）
- [ ] D47 后续（codex 评审 F3，不阻断）：host_asset_name 与 host_keywords 抽 (os, arch) 取参纯函数去 cfg!，linux CI 恢复全平台资产名字面量断言
- [x] stable 封版时随批发版把 README 安装直链与镜像注释从 msvc 名翻 gnu 名（codex 评审 F4，v1.1.5 封版落地：直链与镜像注释与 Move-Item 路径三处全翻）

D30 前目标（2026-09-12 当日闭环归档 P0047，v0.6.1 发版）：四回归修加串面清扫加老用户迁移剧本八步活体全过，清单见 git 历史。

D29 前目标（2026-09-12 当日闭环归档 P0046，v0.6.0 发版；repo 改名 hst-rs 用户已办）清单见 git 历史：

- [x] 阶段 1 至 7 全落（154 单测加 24 集成加三平台矩阵、八条验收、十二资产 digest 双源核对）

D28 前目标（2026-09-11 四轮裁定当日闭环归档 P0045，v0.5.4 发版对端验锚全绿）清单见 git 历史：

- [x] shim 用户级加 session 分键双写与 SessionEnd GC（src\shim.rs）
- [x] 注册四家迁用户级（claude/codex/grok/kimi，src\deploy.rs）
- [x] 状态栏 oma 段读序重排（src\statusline.rs）
- [x] oma hook 用户级分键写与陈旧清理（src\hook.rs）
- [x] init 迁移清理项目 ours 注册与 shim 退役（src\deploy.rs）
- [x] doctor hooks.form 与 state 检查面迁用户级（src\doctor.rs）
- [x] verify 临时用户级注册泛化与判据隔离（src\verify.rs）
- [x] 测试与四门禁全绿；herdr codex review 三轮对齐（151 单测加 24 集成；F1 信任键源根修加 R2 六条清零）
- [x] 文档同步（R002/R004/AGENTS/INDEX/CHANGELOG/COMMAND_MAP/SKILL/S025 追记）加 P0045 归档
- [x] v0.5.4 发版（digest 对端验锚加 herdr 知会 ohmycloud）

挂账：无（M060 两笔 2026-09-11 清讫随 v0.6.0 tag 发出；ome 改 ark hint 串随 v0.6.1 清讫，队列行关账）。

## 前目标清单

> D21 oma diagnose 活性诊断族（2026-09-09 归档 P0041）：cache 双连探测加三连取优加 ds 特判、agents 配置指向加在册加 key 活性加 thinking 对照；131 单元加 21 集成与四门禁全绿；真网关实收。

> D20 去 token 注入收窄（2026-09-09 归档 P0040）：五功能收敛；secrets / providers / login / install / update 五面删除，净删约三千行；124 单元加 21 集成与四门禁全绿。

> D18 状态栏用户级定制（2026-09-09 归档 P0039）：拆段拼装加生成时烘焙，segments / template / icons / codex items 三层键加 --script 整替换；145 单元加 24 集成与四门禁全绿。

> D17 oma agents verify 无头验收（2026-09-08 归档 P0038）：状态栏 mock 直跑加 hook 无头落盘两层判据；本机四家全绿。

> D19 trace 六视图全量恢复（2026-09-08 归档 P0037）：只读检索面回归；原样带回零适配；116 单元加 18 集成全绿。

> D16 oma self update 镜像通道（2026-09-08 归档 P0036）：OMA_MIRROR dev 段边车判新、sha256 强制校验、网络失败回落 GitHub。
> D15 oma 去编排收窄为纯部署配置工具（2026-09-08 归档 P0035）：编排命令与 rmux 后端移除；保留 init / doctor / agents / hook / self update / completions；文档八面同步。
> D14 数据目录改名为 `.oma`（2026-09-07 归档 P0034）：项目与家目录两根同改；旧 `.ohmyagents` 仅旧在则迁；不是 `.omc`。
> D12 任务目录孤儿（2026-09-07 归档 P0033）：`.ohmyagents/t006/` 是 t008 第二轮草稿误落，已删；协议尾注路径显式 `tasks/`。
> D11 状态栏 zig/go/cpp（2026-09-07 归档 P0032）：projKind 扩展；本机临时目录实跑；Grok 仍跳过工具链。
> D10 G005 存量字符清理（2026-09-07 归档 P0031）：SKIP_DIRS 外四类禁字清零，封闭清单删除。
> D08 doctor 检查面补形态（2026-09-07 归档 P0030）：Grok 状态栏 command 三态、JSON hook args 形态；用户实证栏正常。同日 D09：oma 不管种子。
> D07 oma 收窄配合迁册（2026-09-07 归档 P0029）：agents install/update deprecated、agents.toml 冻结历史锚、doctor 四类归 agents 域；当日热修 M044 至 M048。
> 文档体系重构（D01 至 D05，2026-09-03）：全链已完成。
> D06 agent 二进制下装部署五端全量收敛（2026-09-05）：当日闭环后同日方向反转（D07）。

## 队列目标

| 目标 | 状态 | 说明 |
| --- | --- | --- |
| serde 加 validator 加 toml 配置校验选型入档 | 排队 | 用户 2026-09-13 递材并裁「随下批立项」（serde 1 加 validator 0.21 derive 两步校验、toml 1.1 同模型复用、axum-valid Valid 集成、领域层 TryFrom 收口）；候选落 R 系列（R005 家族），适用面 = hst 的 TOML/JSON 配置解析重构候选（statusline.toml 手写 Value 遍历、yolo/doctor 判据面）；立项时走 PRD 澄清范围 |
| wsl 与 Windows config 平移键路径归一 | 排队 | 用户口径（2026-09-13 D39 来函）：wsl 是真 Linux 内核带 `/mnt/c` 邻接，配置在两端平移时项目键路径（如 codex `[projects]`、claude `projects.<abs>`）需归一处理，P0048 D39 节记档；触发面出现时立项 |
| D36 状态栏 HUD 簇 | 排队 | 研究先行已落 S034（流行 HUD 盘点、官方 stdin JSON 契约、多行与 MCP/tools 计数缺口）；设计追问链待启（段取舍、比例口径、多行断行、四家兼容面），随后立项 |
| M060 两笔加 ome 更名尾巴随下个功能版发 | 完成 | M060 两笔（guard 透传白名单化加 shebang 注入缝）随 v0.6.0 tag 发出；ome 改 ark hint 串（shim warn 加 agents hint 已先行为 ark，注释尾巴随批）随 v0.6.1 清讫关账 |
| deploy_shims 用户根旧名清扫 | 排队 | pristine 合并把旧名 shim（oma-state.*）迁进 `~/.hst/hooks` 与新名并存；退役环只扫项目级。P0047 观察面，随下个小版本 |
| 迁移转发 shim 兼容层产品化 | 排队 | 长寿命会话持启动时快照的旧注册，迁移收干 `~/.oma` 后每事件 exit 1（ark codex pane 实况；本机已手动落 `~/.oma/hooks/oma-state.*` 转发层缓解并活体验证）。产品化 = init 或迁移收干时自动落转发层，随 1.1.0 兼容窗同批清（原 v0.7 口径，版本线重开后改号，D34）。P0047 观察面三 |
| 兼容别名一个小版本后删 | 排队 | agents statusline 隐藏别名、oma stub 资产、旧 OMA_* env 读取、旧路径转发 shim 加 `--pretrust` 旧拼写，D29 定的一个版本窗口（原 v0.7，版本线重开后为 1.1.0，D34）清 |
| D13 mac `--version` 一致性 | 完成 | 2026-09-09 lan-mac 经镜像 stable 段直装 v0.3.0 实收：边车 sha256 校验过（digest 与 ohmycloud 对账一致，mirror 第二端点消费实证）、`--version` = oma 0.3.0、agents 四家检测与 `statusline --example` 冒烟绿；旧件备份 `~/.oma/oma-fossil-backup` |
