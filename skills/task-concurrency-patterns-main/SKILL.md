---
name: task-concurrency-patterns
description: 任务并发与依赖管理模式 — 多 Agent/多步骤任务的并发安全、读写分离、依赖编排。适用于：(1) 多 Worker 并行任务 (2) 复杂项目拆分执行 (3) 需要 blocks/blockedBy 依赖关系的工作流。
---

# Task Concurrency Patterns

## 并发安全规则

### 读写分离
```
只读任务（研究/搜索/分析）→ 自由并行，无限制
写操作（实现/编辑/部署）  → 同一文件区域串行
验证任务                  → 可与不同文件区域的实现并行
```

### 并发安全声明

每个任务/工具需声明并发安全性：
- `concurrencySafe: true` → 可以并行执行（如搜索、读取）
- `concurrencySafe: false` → 必须串行（如文件写入、数据库迁移）

## 任务状态机

```
pending → in_progress → completed
```

每个任务包含：
- `subject` — 简短标题
- `description` — 需要做什么
- `activeForm` — 进行时文案（如 "Running tests"）
- `blocks` — 此任务阻塞的任务 ID 列表
- `blockedBy` — 阻塞此任务的任务 ID 列表

## 依赖编排

### 串行依赖
```
Task A (研究) → Task B (实现) → Task C (验证)
B.blockedBy = [A.id]
C.blockedBy = [B.id]
```

### 扇出（并行研究）
```
Task A1 (研究模块1) ─┐
Task A2 (研究模块2) ──┼→ Task B (综合 + 实现)
Task A3 (研究模块3) ─┘
B.blockedBy = [A1.id, A2.id, A3.id]
```

### 扇入（并行实现 + 统一验证）
```
Task B1 (实现模块1) ─┐
Task B2 (实现模块2) ──┼→ Task C (集成验证)
C.blockedBy = [B1.id, B2.id]
```

## 失败处理

1. Worker 失败 → Continue 同一个 Worker（已有错误上下文）
2. 纠正失败 → 换方案，仍然 Continue
3. 三次失败 → 报告用户，提供已尝试的方案列表

## 实际应用模式

### 模式 1: 研究→综合→实现→验证
最常见。研究阶段并行，综合你来做，实现串行，验证独立。

### 模式 2: 快速修复
跳过研究，直接实现+验证。适用于已知问题。

### 模式 3: 大规模重构
多轮迭代：规划→分批实现→每批验证→下一批。
每批内部可并行（不同文件），批间串行。

## 停止正在跑的任务

发现方向错误时，立即停止 Worker：
```
启动 Worker 做 JWT 重构
→ 用户说"别搞 JWT，只修空指针"
→ 停止 Worker
→ Continue 同一个 Worker，给新指令
```

不要让错误方向的 Worker 跑完再修正 — 浪费资源。
