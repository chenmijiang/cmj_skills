# ⚡ Task Concurrency Patterns

**任务并发与依赖管理** — 多 Agent/多步骤任务的并发安全、读写分离、依赖编排。

## 解决什么问题

- 可以并行的研究任务串行执行 → 浪费时间
- 多个 Worker 同时写同一文件 → 冲突覆盖
- 任务之间有依赖但没管理 → 跳过前置步骤
- Worker 跑错方向了不知道停 → 浪费资源

## 核心设计

来自 Claude Code 的 Task System + Coordinator Concurrency Rules：

1. **读写分离** — 只读任务自由并行，写操作同区域串行
2. **任务状态机** — pending → in_progress → completed
3. **依赖编排** — blocks/blockedBy 关系，支持扇出（并行研究）和扇入（统一验证）
4. **失败升级** — Continue（1次）→ 换方案（2次）→ 报告用户（3次）
5. **即时停止** — 发现方向错误立即停 Worker，不等跑完

## 编排模式

```
扇出（并行研究）:
  研究模块A ─┐
  研究模块B ──┼→ 综合 → 实现 → 验证
  研究模块C ─┘

扇入（并行实现）:
  实现模块A ─┐
  实现模块B ──┼→ 集成验证
```
