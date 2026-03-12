# Claude 系列模型训练洞察

## 模型系列

- Claude 1 (2023)
- Claude 2
- Claude 3 (Opus, Sonnet, Haiku)
- Claude 3.5 (Sonnet, Haiku)
- Claude 4 (Opus, Sonnet)

## 训练流程

### 1. 预训练

**数据**：
- 高质量网页文本
- 书籍
- 代码
- 科学论文

**特点**：
- Anthropic 强调"AI safety"数据
- Constitutional AI 方法

### 2. 有监督微调

**数据**：
- 人工标注对话数据
- 拒绝生成有害内容的数据

### 3. RLHF / RLAIF

**方法**：
- RLHF (Reinforcement Learning from Human Feedback)
- RLAIF (AI Feedback)
- Constitutional AI

## 架构特点

- Transformer decoder
- Anthropic 特有的安全层
- 工具使用能力 (Function Calling)
- Computer Use 能力

## 数据格式

```json
{
  "conversations": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ],
  "safety_labels": [...]
}
```

## 训练技巧

- RLHF with PPO
- 人类反馈排序
- 红队测试 (Red Teaming)
- Constitution (行为准则)
