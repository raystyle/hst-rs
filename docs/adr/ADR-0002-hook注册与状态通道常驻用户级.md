---
id: ADR-0002
title: hook注册与状态通道常驻用户级
status: accepted
date: 2026-09-15
deciders:
  - raystyle
supersedes: []
superseded_by: None
tags: ['hook', 'D27', 'D28', 'D52']
---

# ADR-0002:hook注册与状态通道常驻用户级

## Context

项目级 hook 注册导致跨项目状态栏失效：未 init 项目无状态数据，共享项目配置互踢。用户 2026-09-11 裁 D28「hook 应用户全局」。

## Decision

四家注册面全部用户层（claude settings.json、codex hooks.json 加 config.toml 信任键、grok global 层、kimi 用户 config.toml）；shim 自包含常驻 ~/.hst/hooks/（D27 解耦，零 hst 依赖可无痛轮换）；状态按 session 分键写 ~/.hst/state/；项目级 ours 注册由 init 退役。家目录不是项目（D52 守卫：root==user_home 时项目退役趟整组跳过）。

## Consequences

外来 hook 永不碰（herdr 等共存实证 S035）；跨侧共享项目配置不互踢（per-OS 字段各侧只写本侧）；旧注册形态按 stem 与 shim 名家族识别收敛。
