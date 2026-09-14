# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D48 stable 通道镜像腿与 GitHub API 限流自救

> 澄清一轮已裁（PRD D48 第 1 轮）。依据：ark `src\selfupdate.rs` 三层读序取证（mirror_first 显式开关、official_asset_meta 主名先兼容名次、mirror_attempts 段读序表、mirror_fallback_meta 首个在位边车落锚、download 层镜像兜底、GH_TOKEN Bearer 注入）；ohmycloud 边车事实（hst/stable 与 hst/dev 同构 sha256sum 边车，CI Package 步上传）；S028 dev 镜像通道既有机制。四段：

1. **GH_TOKEN 附加（src\update.rs）**：`fetch_release` 在 `GH_TOKEN` 在位时附 `Authorization: Bearer`（ark resolve.rs 同款语义，匿名 60 到 5000 次/时）；鉴权头构造抽纯函数（`Option<&str>` 入参）可单测；S028「GH_TOKEN 自动附带」旧记随批更正。
2. **stable 镜像腿（src\update.rs）**：镜像 URL 构造器 `mirror_sidecar_url` 加段参（`hst/dev` 硬编码改 `hst/{seg}`，seg 随通道、dev 禁回落 stable）；新增 stable_via_mirror：deterministic 名 `host_asset_name()` 取边车 digest、对 `selfupdate.json` 安装记录判新（同 dev 机制：一致即 current、不同保守更新、无记录保守更新；`--force` 跳过）、下载 sha256 强校验、Windows rename 舞步复用；stable 安装路径补记 digest 入 selfupdate.json（GitHub 腿与镜像腿都记，判新锚的数据面）。
3. **读序与缺省回退（src\update.rs）**：`HST_MIRROR` 设值 = 基址覆盖加 mirror-first 两通道（D16 语义扩 stable，dev 现行为不变）；未设 = GitHub 优先，fetch 失败（403 限流与网络类）自动回退镜像腿用默认基址 `env.ohmygh.com`；空串 = 镜像全关。回退判定抽纯函数（镜像计划三态：Off / DefaultFallback(base) / First(base)）可单测。
4. **测试与门禁**：纯函数单测（鉴权头、段化 URL 构造 stable 与 dev 双形、镜像计划三态、stable digest 判新三态：current / update / 无记录保守）；既有 dev 镜像用例回归不动；`cargo test --locked` 全绿、fmt、md 四件套；herdr 右侧 codex review 对齐（self update 是断源救险面，沿 D47 惯例）后推 main；封版时点另裁（不推 tag）。文档随批：R002 self update 行、S028 D48 追记、INDEX update.rs 行 D48 标签。
