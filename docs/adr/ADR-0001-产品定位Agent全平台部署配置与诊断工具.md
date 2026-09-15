---
id: ADR-0001
title: 产品定位Agent全平台部署配置与诊断工具
status: accepted
date: 2026-09-15
deciders:
  - raystyle
supersedes: ['P0004（旧定位归档件）']
superseded_by: None
tags: ['定位']
---

# ADR-0001:产品定位Agent全平台部署配置与诊断工具

## Context

原编排定位（rmux 多路复用任务编排器，P0004）与产品演进脱节：用户 2026-09-08 裁定 D15 去编排，spawn/send/status/serve/mcp 与 rmux 后端整体移除；2026-09-09 D20 再裁去 token 注入。五功能聚焦：agent 可用性诊断、hook 设置、状态栏设置、对话 trace、yolo 不阻塞设置。

## Decision

HST 定位为 Agent 全平台部署配置与诊断工具，专注五功能；编排归档 P0035、token 注入归档 P0040；四仓分工（ark-rs 装部署、hst 治理、ohmypwsh 密钥、ohmycloud 分发）。

## Consequences

trace 直读原生会话库与 rmux 零耦合（D19 恢复）；agent 二进制下装归 ark（D07/D09）；跨仓协作走 herdr 会话不发 issue。替代上一版定位（P0004，已 superseded）。
