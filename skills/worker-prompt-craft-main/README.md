# ✍️ Worker Prompt Craft

**子代理 Prompt 编写指南** — 写出让子代理一次做对的 prompt，不返工。

## 解决什么问题

- 子代理看不到父对话上下文 → 指令模糊导致做错
- "修复那个 bug" → 哪个 bug？哪个文件？什么行为？
- "基于之前的研究" → 子代理根本不知道之前研究了什么

## 核心设计

来自 Claude Code 的 Coordinator Prompt Guidelines：

1. **自包含原则** — 每个 prompt 包含所有必要信息（文件路径、行号、错误信息）
2. **目的陈述** — 告诉 Worker 这个任务的用途，帮它校准深度
3. **完成标准** — 明确"做完"长什么样
4. **反模式清单** — 列出绝对禁止的模糊写法

## 正确 vs 错误对比

```
❌ "修复我们讨论的 bug"
✅ "修复 src/auth/validate.ts:42 的空指针。Session 过期时 user 为
   undefined。加 null check，null 则返回 401。提交并报告 hash。"

❌ "创建最近变更的 PR"
✅ "从 main 创建分支 'fix/session-expiry'，cherry-pick commit abc123，
   push 并创建 draft PR，添加 team 为 reviewer，报告 PR URL。"
```
