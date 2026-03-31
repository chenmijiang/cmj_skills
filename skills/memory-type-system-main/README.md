# 📝 Memory Type System

**结构化记忆类型系统** — 四类型分类 + frontmatter 规范 + 写入/召回/漂移防护全流程。

## 解决什么问题

- 记忆混乱无分类 → 什么都往一个文件里塞
- 召回不准 → 检索出一堆无关内容
- 膨胀失控 → MEMORY.md 越来越大但信噪比越来越低
- 过时记忆误导 → "记忆说函数在 line 42" 但代码早改了

## 核心设计

来自 Claude Code 的 Memory Type Taxonomy（完整四类型体系）：

| 类型 | 内容 | 示例 |
|---|---|---|
| `user` | 用户角色/偏好/知识 | "用户是资深 Go 开发，React 新手" |
| `feedback` | 纠正和确认（带 Why） | "测试不要 mock DB，因为上次 mock 通过但线上挂了" |
| `project` | 进行中的项目/决策 | "代码冻结 3/5 开始，移动端要切 release 分支" |
| `reference` | 外部系统指针 | "管道 bug 追踪在 Linear 项目 INGEST" |

### 关键创新

1. **NOT-to-save 比 to-save 更重要** — 明确列出不该记的 6 类内容
2. **每条记忆带 frontmatter** — name + description + type，支持语义召回
3. **反馈记忆必须带 Why + How to apply** — 否则遇到边界情况会做错
4. **MEMORY.md 是索引不是记忆** — 上限 200 行，每条一行
