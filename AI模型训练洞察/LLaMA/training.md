# LLaMA 系列模型训练洞察

## 模型系列

- LLaMA (7B, 13B, 65B)
- LLaMA 2 (7B, 13B, 70B)
- LLaMA 3 (8B, 70B, 400B)
- LLaMA 3.1 (8B, 70B, 405B)
- CodeLLaMA
- LLaMA Guard

## 训练流程

### 1. 预训练

**数据**：
- LLaMA 1: 1.4T tokens (Common Crawl, GitHub, Wikipedia, Books)
- LLaMA 2: 2T tokens
- LLaMA 3: 15T tokens

**特点**：
- 大量代码数据 (LLaMA 3)
- 多语言支持
- 高质量数据过滤

### 2. 有监督微调

**数据**：
- 人工标注指令数据
- 公开数据集: Alpaca, ShareGPT

### 3. DPO (Direct Preference Optimization)

**替代 RLHF**：
- 更简单稳定
- 不需要 reward model

## 架构特点

- RMSNorm
- SwiGLU 激活函数
- Rotary Position Embedding (RoPE)
- Grouped Query Attention (GQA)

## 训练资源

| 模型 | GPU | GPU Hours |
|------|-----|-----------|
| LLaMA 7B | 8x A100 | 82K |
| LLaMA 13B | 8x A100 | 135K |
| LLaMA 65B | 8x A100 | 1M |

## 数据格式

```
{"text": "...", "source": "common_crawl"}
```
