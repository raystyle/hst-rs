# PLAN：当前目标实施计划

> 角色：**当前目标方案文档**：基于 `docs\research\`（为什么）与 `docs\references\`（怎么做）撰写的执行计划；每条挂依据来源，随目标变化更新，不存历史目标。
> 分工：`PRD.md` = 要什么；`TODO.md` = 做到哪；本文件 = 怎么做；通用工作流见 `docs\guide\G003-工作流标准细则-从登记到归档五步.md`。

## 当前目标：D47 Windows 构建切 gnu 交叉编译

> 用户裁 2026-09-14 摆脱 VC。依据：PRD D47；背景实证 ohmycloud 侧已验（WSL 加 rustup target 加 apt mingw-w64 后交叉一次过、产物部署实跑 --version 与 doctor 全过、CRT 静态零 DLL 依赖、12.1MB 与 msvc 同量级），本机 mingw 与 gnu target 已装、复验一次过；S028 资产命名约定（资产名即编译目标三元组）。三段：

1. **CI 交叉岗（.github\workflows\dev-release.yml）**：windows-latest 的 x86_64-pc-windows-msvc 岗换 ubuntu-latest 交叉岗（target x86_64-pc-windows-gnu、archive zip）；apt mingw-w64 步按 `matrix.target == 'x86_64-pc-windows-gnu'` 条件装；Test 步对交叉岗跳过（PE 不可在 linux 跑，测试面由 linux/mac 双岗覆盖，注释注明）；Package 的 exe 后缀判定从 `os=windows-latest` 改判 triple（`*-pc-windows-*` 加 .exe）；rust-cache key 走 target 自动隔离不受影响。
2. **self update 切 gnu（src\update.rs）**：host_asset_name 的 windows 臂改 `hst-{arch}-pc-windows-gnu.zip`（镜像边车 URL 随资产名走）；host_keywords 的 windows 臂改 `["windows-gnu","windows-msvc","windows"]`：新源选 gnu、旧 release 仅 msvc 资产时回落命中、通用 windows 词保底旧 msvc 二进制升级新源；注释记 2026-09-14 切 gnu 摆脱 VC 裁定与 stable 封版前旧名共存口径。
3. **测试与门禁**：host_asset_name_matches_release_convention 的 windows 两臂断言改 gnu 名；picks_host_asset_from_convention 资产列表 msvc 换 gnu 并断言 windows 选 gnu，同测补 msvc-only 列表回落断言（同一 release 不与新 msvc 并存，pick 按列表序取首个关键词命中）；验收 = cargo test --locked 全绿、md 四件套、提交推 main 后 dev 滚动源自动出 gnu 资产，回报 CI 绿与资产名列表。不动版本号、不推 tag（stable 封版另裁；随封版把 README 的 stable 直链与镜像注释从 msvc 名翻 gnu 名，避免提前 404）。
