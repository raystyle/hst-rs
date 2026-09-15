---
id: ADR-0003
title: Windows构建切gnu交叉编译摆脱VC
status: accepted
date: 2026-09-15
deciders:
  - raystyle
supersedes: []
superseded_by: None
tags: ['构建', 'D47']
---

# ADR-0003:Windows构建切gnu交叉编译摆脱VC

## Context

msvc 岗依赖 Windows runner 与 VC 工具链；ohmycloud 裸环境实证 WSL 加 rustup target 加 mingw-w64 交叉一次过（CRT 静态零 DLL 依赖，12.1MB 同量级）。用户 2026-09-14 裁 D47。

## Decision

CI windows-latest msvc 岗换 ubuntu-latest gnu 交叉岗（apt mingw-w64、Test 交叉岗跳过由 linux/mac 双岗覆盖）；Windows 资产名 hst-arch-pc-windows-gnu.zip；self update 关键词梯子 windows-gnu 优先加 msvc 回落加通用词保底。

## Consequences

产物 PE32+ console 零 DLL 依赖；旧 release 仅 msvc 资产由回落词兜住升级；cfg(windows) 分支断言在 linux CI 不参与编译（测试钉记 REQ 队列）。
