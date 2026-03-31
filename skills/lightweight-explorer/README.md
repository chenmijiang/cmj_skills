# 🔍 Lightweight Explorer

**轻量探索模式** — 用最少 token 完成代码搜索和调研。只读、快速、低成本。

## 解决什么问题

- 搜索代码时加载了全部工具和项目规则 → 浪费大量 context
- 只是想找个文件，却用了大模型 → 浪费成本
- 串行搜索 → 本可以并行的 grep 一个一个跑

## 核心设计

来自 Claude Code 的 Explore Agent 架构：

1. **严格只读** — 禁止创建/修改/删除任何文件
2. **搜索策略分级** — quick（基础）/ medium（多角度）/ thorough（全面）
3. **强制并行** — 独立的搜索操作必须同时发起
4. **省 context** — 不加载项目规则文件（CLAUDE.md），只做搜索

## 设计理念

> Claude Code 的 Explore Agent 用 Haiku（最小模型）运行，并且 `omitClaudeMd: true` 跳过项目规则加载。只读搜索根本不需要大模型和完整上下文。
