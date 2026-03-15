# Skillet

面向 Creator / Designer / Prosumer 的 **Skill Hub & Skill Bundle 发现与搜索引擎**。

## Product Positioning

Skillet 的产品本体是「发现与搜索」，不是执行端或入口端：

- 聚合多 Hub 的 Skill / Bundle 元数据
- 建立统一索引，支持检索、过滤、排序
- 输出可被下游消费的结果（OpenClaw、Web、CLI 等）

IM 是用户的原生入口，Skillet 是其后端市场能力，不封装 IM 生态位本身。

## Scope

### In Scope

1. Multi-Hub catalog ingestion（LobeHub、CreatorSkills、ClawHub 等）
2. Skill/Bundle normalization（统一 schema）
3. Intent-aware search and ranking（结合 SkillRank + Prism）
4. Bundle-first discovery（优先完整工作流包）

### Out of Scope (V1)

1. IM runtime / bot gateway
2. 内容生成执行器
3. 多平台发布器

## Architecture

| Dependency | Role |
|------------|------|
| **SkillRank** | Hub/Domain 排名信号 |
| **Prism** | 语义图谱与检索增强 |

Skillet 负责将两者产品化为可查询的「Skill/Bundle 发现层」。

## V1 MVP

V1 目标不是执行内容，而是可用搜索结果：

1. 输入自然语言任务（如“适合小红书复用的视频二创 bundle”）
2. 返回可安装/可购买/可使用的 Skill 或 Bundle
3. 给出排序理由与来源 Hub

## Development

```bash
pnpm install
pnpm dev
pnpm test
pnpm doc:governance
```

## Documentation

- [ADRs](doc/adr/) — Architectural decisions
- [AGENTS.md](AGENTS.md) — Mandatory rules for AI agents and developers

## License

MIT
