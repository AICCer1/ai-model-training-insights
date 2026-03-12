# GPT 系列模型训练洞察

## 模型系列

- GPT-1 (117M)
- GPT-2 (1.5B)
- GPT-3 (175B)
- GPT-3.5 (InstructGPT, ChatGPT)
- GPT-4 (1.7T MoE)
- GPT-4o / GPT-4o mini
- GPT-5 (最新)

## 训练流程

### 1. 预训练 (Pre-training)

**目标**：让模型学习通用语言表示

**数据**：
- WebText, Common Crawl, BooksCorpus, Wikipedia
- 数据量：TB级别
- 清洗：去重、过滤低质量内容

**方法**：
- Next Token Prediction (NTP)
- Large-scale transformer 训练
- 分布式训练 (DeepSpeed, Megatron)

### 2. 有监督微调 (SFT)

**目标**：指令跟随能力

**数据**：
- 人工标注的指令-响应对
- 格式：User/Assistant 对话

### 3. 强化学习对齐 (RLHF)

**阶段**：
1. Reward Model 训练
2. PPO 优化

**数据**：
- 人类反馈排序数据

## 数据格式

```
{
  "text": "用户输入",
  "completion": "模型输出",
  "messages": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ]
}
```

## 训练技巧

- Learning rate warmup
- Gradient clipping
- AdamW optimizer
- ZeRO optimization
- Mixed precision training
