# AI模型训练洞察项目

本项目聚焦主流大模型的训练流程、数据准备、对齐方法与系统特征，按模型隔离文档，便于纵向阅读和横向比较。

## 目录结构

- `GPT/` - GPT 系列训练洞察
- `LLaMA/` - LLaMA 系列训练洞察
- `Claude/` - Claude 系列训练洞察
- `DeepSeek/` - DeepSeek 系列训练洞察
- `GLM/` - GLM 系列训练洞察
- `QWEN/` - Qwen 系列训练洞察
- `MiniMax/` - MiniMax 系列训练洞察
- `闭源模型/` - Grok、Gemini 等闭源模型训练洞察
- `common/` - 通用主题与横向整理

## 推荐阅读顺序

1. 先看各模型目录下的 `training.md`，了解单一模型的训练流程
2. 再看 `common/model-comparison.md`，做横向对比
3. 结合 `common/data-preparation.md`、`common/training-techniques.md` 理解通用方法

## 包含内容

- 各模型训练流程详解
- 数据来源、清洗与去重策略
- SFT / RLHF / DPO / GRPO 等后训练方法整理
- 架构特点、长上下文与工具使用能力概览
- 横向对比页与参考资料索引

## 说明

- 不同模型的公开程度差异很大，部分信息只能视作公开推断或行业整理
- 闭源模型条目应优先关注“公开披露内容”与“推测边界”
- 本仓库更适合作为研究笔记与资料导航，而不是官方定论

## 持续更新中...

---

*本项目用于 AI 生产线平台洞察研究*
