# 训练技巧通用指南

## 优化器

| 优化器 | 特点 |
|--------|------|
| AdamW | 主流选择 |
| AdamW + 8-bit | 内存优化 |
| LAMB | 大 batch size |
| Sophia | 新方法，理论上更快 |

## 学习率调度

```
1. Warmup (5-10% 训练步数)
2. Cosine Decay
3. Linear Decay
4. Constant + Decay
```

## 分布式训练

### 数据并行

- DeepSpeed ZeRO
- FSDP (Fairscale)

### 模型并行

- Pipeline Parallelism
- Tensor Parallelism

### 关键技术

- Gradient Checkpointing
- Mixed Precision (FP16/BF16)
- Gradient Accumulation

## 超参数

| 参数 | 常见值 |
|------|--------|
| Batch Size | 32-512 per device |
| Learning Rate | 1e-4 ~ 1e-5 |
| Warmup Steps | 500-2000 |
| Context Length | 4K, 8K, 32K, 128K |

## 训练稳定性

- Gradient Clipping (max_norm=1.0)
- Loss Spike 处理
- NaN 检测
- Early Stopping
