---
name: smart-compact
description: "Smart context compaction for OpenClaw agents. Inspired by Claude Code's 3-phase compaction strategy (Micro → Standard → Session Memory). Before running /compact, this skill scans tool outputs, extracts valuable information into memory files, and generates a pre-compact checklist — ensuring nothing important is lost during compression. Triggers: smart-compact, 智能压缩, pre-compact, 压缩检查, compact check. Can also run automatically before each /compact."
---

# Smart Compact — 智能压缩增强

灵感来源：Claude Code 源码中的三阶段压缩策略（Micro Compact → Standard Compact → Session Memory Compact）。

## 什么时候用

- 用户说"智能压缩"、"smart-compact"、"压缩检查"
- 在手动执行 /compact 之前先跑一遍
- 对话上下文快满时，主动触发
- Heartbeat 检测到 context 接近 80% 时自动建议

## 核心理念

Claude Code 的压缩不是一股脑全压，而是**分三步走**：

1. **Micro Compact**：只压缩工具输出（最省 token 的方式）
2. **Standard Compact**：压缩整个对话历史
3. **Session Memory Compact**：压缩时自动提取记忆

OpenClaw 原生的 /compact 是一步到位的，会丢失细节。Smart Compact 在 /compact 之前插入一个"预处理"阶段，**先救再压**。

## 执行流程

### Phase 1 — 扫描工具输出（Micro Compact 思路）

1. 回顾当前对话中所有的工具调用结果（exec、read、web_fetch、web_search 等）
2. 识别**大块输出**（超过 50 行或 2000 字符的工具结果）
3. 对每个大块输出评估：
   - 是否包含**关键信息**（决策、配置、错误信息、URL、凭证等）
   - 是否已经被后续对话**引用或总结过**
   - 是否是**重复或冗余**的（如多次 ls、git status）

### Phase 2 — 提取记忆（Session Memory 思路）

1. 从工具输出和对话中提取值得持久化的信息：
   - **新发现的事实**：IP 地址、配置值、API 端点、文件路径
   - **决策和原因**：为什么选了方案 A 而不是 B
   - **错误和解决方案**：踩坑记录
   - **用户偏好**：明确表达的喜好或要求
   - **任务进度**：哪些做完了，哪些还没做

2. 将提取的信息**追加写入** `memory/YYYY-MM-DD.md`
   - 使用 `edit`（追加模式），绝不覆盖已有内容
   - 每条记忆附带简短的来源说明

### Phase 3 — 生成压缩前检查清单

输出一份结构化的检查清单，格式如下：

```
📋 Smart Compact 检查清单
━━━━━━━━━━━━━━━━━━━━━━

📊 扫描统计：
- 工具调用总数：N 次
- 大块输出（>50行）：N 个
- 已引用/总结过的：N 个
- 可安全压缩的：N 个

💾 已提取到记忆：
- [+] 新事实：简要描述...
- [+] 决策记录：简要描述...
- [+] 错误解决：简要描述...
（共 N 条写入 memory/YYYY-MM-DD.md）

⚠️ 需要注意：
- [!] 某某工具输出包含重要数据但尚未被引用
- [!] 某某配置值只出现在工具输出中

✅ 建议：可以安全执行 /compact
```

### Phase 4 — 执行压缩（可选）

- 如果检查清单显示"✅ 可以安全压缩"，提示用户确认
- 用户确认后，执行 /compact
- 如果有 ⚠️ 警告项，先处理完再压缩

## 安全规则

### 必须遵守
- **绝不丢弃未被记录的关键信息**：宁可多存也不能漏存
- **追加写入**：只用 edit 追加到 memory 文件，绝不覆盖
- **不自动压缩**：除非用户明确确认，否则只生成检查清单
- **透明**：每一步操作都告知用户

### 信息分类标准
- **必须保存**：凭证、API Key、IP 地址、配置值、文件路径、错误解决方案
- **建议保存**：决策原因、用户偏好、任务进度
- **可以丢弃**：重复的 ls 输出、已被总结的搜索结果、中间调试过程

## 与 Dream Skill 的配合

Smart Compact 和 Dream 是互补的：
- **Smart Compact**：实时的，在压缩前抢救信息 → 写入日记
- **Dream**：定期的，把日记整合到长期记忆 → 更新 MEMORY.md

推荐工作流：
1. 对话中随时触发 Smart Compact 保护信息
2. 每天凌晨 Dream 整合日记到长期记忆
3. 形成完整的记忆保护链条
