---
id: REQ-003
title: self-update读序不回环集成断言
status: draft
priority: should
trace: null
---

# REQ-003:self-update读序不回环集成断言

## Scenario

run 的读序分支收束（mirror-first 失败后 GitHub 再失败不回环、DefaultFallback 才触发回退）只有两轮真网 e2e 佐证，无断言级测试（codex D48 评审 F3）。

## Criteria

- [ ] 假基址集成断言钉「不回环」性质
- [ ] 三态读序分支覆盖
