# 数据准备通用指南

## 数据类型

### 1. 文本数据

| 来源 | 特点 |
|------|------|
| Common Crawl | 大规模，多语言，质量不一 |
| WebText | 高质量，付费 |
| Wikipedia | 结构化，高质量 |
| Books | 连续性强 |
| GitHub | 代码数据 |

### 2. 代码数据

- StarCoder
- The Stack
- CodeSearchNet

### 3. 对话数据

- ShareGPT
- Anthropic HH-RLHF
- OpenAssistant

## 数据清洗

### 质量过滤

```
1. 语言检测 (langdetect)
2. 质量评分 (Perplexity)
3. 重复检测 (MinHash, SimHash)
4. 敏感内容过滤
```

### 去重

- 精确去重 (哈希)
- 近似去重 (MinHash)
- 文档级/句子级

## 数据格式

### 训练格式 (JSONL)

```json
{"text": "The quick brown fox..."}
{"messages": [{"role": "user", "content": "..."}]}
```

### HuggingFace Dataset

```python
from datasets import Dataset

dataset = Dataset.from_dict({
    "text": [...]
})
```

## 数据配比

常见配比：
- 60-70% 网页文本
- 15-20% 代码
- 10-15% 书籍
- 5% 对话
