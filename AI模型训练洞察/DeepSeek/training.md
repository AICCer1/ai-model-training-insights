# DeepSeek 系列模型训练洞察

## 模型系列

- **DeepSeek V2** (2024年5月) - 强大的MoE模型
- **DeepSeek V3** (2024年12月) - 全面升级，671B参数MoE
- **DeepSeek Coder** (2024年) - 代码专用模型
- **DeepSeek Math** (2024年) - 数学推理专用模型
- **DeepSeek V2.5** (2024年) - 合并版，文理双修

## 训练流程

### 1. 预训练 (Pre-training)

#### 数据来源与配比

| 版本 | 预训练 Token 数 | 数据来源 |
|------|----------------|----------|
| DeepSeek V2 | ~2.4T | 网页、代码、书籍、论文 |
| DeepSeek V3 | >14T | 多源大规模高质量数据 |
| DeepSeek Coder | ~1T+ | 代码仓库、代码竞赛 |
| DeepSeek Math | 500B+ | 数学题库 |

**Deep论文、竞赛Seek V3 数据配比**：
- **英文**：30%
- **中文**：30%
- **代码**：20%
- **数学/科学**：15%
- **其他**：5%

#### 数据清洗方法

1. **质量过滤**：
   - 基于语言识别的质量分类
   - 重复内容检测
   - 低质量文档过滤

2. **去重**：
   - 文档级SHA256哈希去重
   - 近似去重

3. **分词器**：
   - **Byte-level BPE**
   - **词表大小**：约100k

#### 训练配置

| 模型 | 总参数 | 激活参数 | 专家数 | 上下文长度 |
|------|--------|----------|--------|-----------|
| DeepSeek V2 | 236B | 21B | 8 | 128K |
| DeepSeek V2 Lite | 16B | 2.4B | 8 | 32K |
| DeepSeek V3 | 671B | 37B | 8 | 128K |
| DeepSeek Coder | 33B | - | - | 16K |
| DeepSeek Math | 67B | - | - | 4K |

**DeepSeek V3 训练超参数**：
- **优化器**：AdamW (β1=0.9, β2=0.95)
- **学习率**：峰值 2e-4
- **学习率调度**：Cosine Annealing
- **批量大小**：动态调整
- **梯度裁剪**：1.0
- **专家并行**：EP (Expert Parallelism)

### 2. 有监督微调 (SFT)

#### 数据来源

| 数据集 | 数量 | 描述 |
|--------|------|------|
| DeepSeek-Clean | 数十万 | 高质量指令对话 |
| 公开指令数据 | 混合 | 开源SFT数据 |

#### 训练配置
- **学习率**：1e-5 ~ 5e-6
- **Epochs**：2
- **序列长度**：4K / 8K

### 3. 对齐训练 (RLHF / GRPO)

#### DeepSeek-V3对齐方法

**GRPO (Group Relative Policy Optimization)**：
- 无需显式奖励模型
- 组内相对排序优化
- 更稳定的训练

```python
# GRPO损失函数
L = -log(σ(r_chosen - r_rejected))
```

#### DeepSeek Math特殊训练
- **PPO** + **组织级强化学习**
- 数学推理链(Chain-of-Thought)优化

## 架构特点

### 1. MoE (Mixture of Experts) 架构

**DeepSeek V2/V3 核心**：
- **细粒度专家划分**：8个专家，更灵活的激活
- **共享专家**：捕获通用知识
- **路由机制**：Top-K 路由策略

```
输入 → 门控路由 → 选中专家 → 输出
              ↓
         共享专家 → 辅助输出
```

### 2. MLA (Multi-head Latent Attention)

**特点**：
- 键值(Key-Value)压缩
- 大幅降低推理显存
- 保持注意力质量

### 3. 位置编码

- **RoPE**：旋转位置编码
- **DeepSeek-V3**：增强版RoPE

### 4. 训练优化

- **FP8混合精度训练**
- **DualPipe**：前向/后向重叠
- **跨节点专家并行**

### 5. 激活函数

- **SwiGLU**

## DeepSeek Coder/Math 特性

### DeepSeek Coder
- **代码补全**
- **代码修复**
- **项目级理解**
- **多语言支持**

### DeepSeek Math
- **数学推理**
- **步骤级思考**
- **LaTeX渲染支持**

## 训练资源

| 模型 | 规模 | GPU Hours | Token数 |
|------|------|-----------|---------|
| DeepSeek V2 | 236B | ~100K | 2.4T |
| DeepSeek V3 | 671B | ~700K | 14T+ |
| DeepSeek Coder | 33B | ~30K | 1T+ |
| DeepSeek Math | 67B | ~50K | 500B+ |

## 数据格式

### 预训练格式
```json
{"text": "文本内容", "data_type": "code|web|book"}
```

### SFT格式
```json
{
  "messages": [
    {"role": "user", "content": "问题"},
    {"role": "assistant", "content": "回答"}
  ]
}
```

### GRPO/DPO格式
```json
{
  "prompt": "问题",
  "chosen": "好回答",
  "rejected": "差回答"
}
```

## 参考文献

1. **DeepSeek-V2**: https://arxiv.org/abs/2405.04434
2. **DeepSeek-V3**: https://github.com/deepseek-ai/DeepSeek-V3
3. **DeepSeek Coder**: https://github.com/deepseek-ai/deepseek-coder
4. **DeepSeek Math**: https://github.com/deepseek-ai/deepseek-math
5. **DeepSeek官网**: https://www.deepseek.com/

---

*最后更新：2025*
