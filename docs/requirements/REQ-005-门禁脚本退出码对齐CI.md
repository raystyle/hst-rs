---
id: REQ-005
title: 门禁脚本退出码对齐CI
status: draft
priority: should
trace: null
---

# REQ-005:门禁脚本退出码对齐CI

## Scenario

mdcharlint 与 md-heading-scan 本地违规退出码为 0，命令链拦不住（GOAL 破折号两度靠 CI 兜底）。

## Criteria

- [ ] 两脚本违规时退出码改 1（与 CI docs-gate 行为一致）
- [ ] 本地链路复跑验证
