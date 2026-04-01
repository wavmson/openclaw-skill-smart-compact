# 🔍 Smart Compact — 智能压缩增强

> 灵感来自 Claude Code 源码的三阶段压缩策略，让 OpenClaw 的 /compact 不再丢失重要信息。

## 中文说明

### 这是什么？

OpenClaw 的 `/compact` 命令会压缩对话上下文来节省 token，但压缩过程中可能**丢失重要信息**——比如工具输出中的配置值、API 端点、错误解决方案等。

Smart Compact 在 `/compact` 之前插入一个"预处理"阶段：**先把重要信息救出来，再压缩**。

### 灵感来源

Claude Code 的压缩不是一股脑全压，而是分三步：

| 阶段 | Claude Code | Smart Compact 对应 |
|------|------------|-------------------|
| 1️⃣ Micro Compact | 只压缩工具输出 | 扫描大块工具输出 |
| 2️⃣ Standard Compact | 压缩整个对话 | 生成压缩检查清单 |
| 3️⃣ Session Memory | 提取记忆 | 写入 memory 文件 |

### 安装

**方式一（推荐）：**

```bash
clawhub install smart-compact
```

**方式二：从 GitHub 安装**

```bash
git clone https://github.com/wavmson/openclaw-skill-smart-compact.git ~/.openclaw/skills/smart-compact
```

安装后重启 Gateway：`openclaw gateway restart`

### 使用方法

对你的 Agent 说：
- "智能压缩" 或 "smart-compact"
- "压缩检查" 或 "compact check"
- "pre-compact"

### 执行流程

```
Phase 1: 扫描工具输出 → 识别大块输出和关键信息
    ↓
Phase 2: 提取记忆 → 写入 memory/YYYY-MM-DD.md
    ↓
Phase 3: 生成检查清单 → 显示统计和警告
    ↓
Phase 4: 用户确认 → 执行 /compact
```

### 输出示例

```
📋 Smart Compact 检查清单
━━━━━━━━━━━━━━━━━━━━━━

📊 扫描统计：
- 工具调用总数：23 次
- 大块输出（>50行）：5 个
- 已引用/总结过的：3 个
- 可安全压缩的：4 个

💾 已提取到记忆：
- [+] Docker 容器 xiaohongshu-mcp 端口 18060
- [+] GitHub PAT 已配置到 credential store
- [+] 小红书发布正文限制 1000 字
（共 3 条写入 memory/2026-04-02.md）

⚠️ 需要注意：
- [!] exec 输出包含 API 响应但未被引用

✅ 建议：可以安全执行 /compact
```

### 与 Dream Skill 的配合

| Skill | 时机 | 作用 |
|-------|------|------|
| Smart Compact | 实时（压缩前） | 从对话中抢救信息 → 写入日记 |
| Dream | 定期（每天凌晨） | 把日记整合到长期记忆 → 更新 MEMORY.md |

两者形成完整的**记忆保护链条**：
1. Smart Compact 保证压缩不丢信息
2. Dream 保证日记被整合到长期记忆

---

## English

### What is this?

OpenClaw's `/compact` command compresses conversation context to save tokens, but important information can be **lost** during compression — config values, API endpoints, error solutions buried in tool outputs.

Smart Compact adds a "pre-processing" phase before `/compact`: **rescue important info first, then compress**.

### Inspiration

Claude Code's compaction uses a 3-phase strategy:

| Phase | Claude Code | Smart Compact |
|-------|------------|---------------|
| 1️⃣ Micro Compact | Compress tool outputs only | Scan large tool outputs |
| 2️⃣ Standard Compact | Compress full conversation | Generate pre-compact checklist |
| 3️⃣ Session Memory | Extract memories | Write to memory files |

### Install

**Option A (recommended):**

```bash
clawhub install smart-compact
```

**Option B: From GitHub**

```bash
git clone https://github.com/wavmson/openclaw-skill-smart-compact.git ~/.openclaw/skills/smart-compact
```

Then restart: `openclaw gateway restart`

### Usage

Say to your agent:
- "smart-compact" or "pre-compact"
- "compact check"

### How it works

```
Phase 1: Scan tool outputs → identify large outputs & key info
    ↓
Phase 2: Extract memories → write to memory/YYYY-MM-DD.md
    ↓
Phase 3: Generate checklist → show stats and warnings
    ↓
Phase 4: User confirms → execute /compact
```

### Works with Dream Skill

- **Smart Compact**: Real-time, rescues info before compression → writes to daily logs
- **Dream**: Periodic, consolidates daily logs into long-term memory → updates MEMORY.md

Together they form a complete **memory protection chain**.

## License

MIT — See [LICENSE](LICENSE)

## Author

[@wavmson](https://github.com/wavmson)
