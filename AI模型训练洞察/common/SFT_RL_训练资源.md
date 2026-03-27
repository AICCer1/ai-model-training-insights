# SFT / RL 训练资源详细数据

> 收集主流大模型在 SFT（有监督微调）和 RL（强化学习）对齐阶段的训练资源、数据规模、以及模型资源消耗对比。

---

## 1. InstructGPT (GPT-3.5 对齐)

**来源**: Ouyang et al., 2022, "Training language models to follow instructions with human feedback"  
**论文**: [arXiv:2203.02155](https://arxiv.org/abs/2203.02155)

### SFT 阶段

| 项目 | 数据量 | 说明 |
|------|--------|------|
| Labeler 演示数据 | ~14.7K | 人工标注者写的示范回答 |
| 来源 | Labeler-written + API prompts | 标注者编写 + API用户提交 |

### Reward Model (RM) 阶段

| 项目 | 数据量 | 说明 |
|------|--------|------|
| Pairwise Comparisons | ~33.3K | 人类偏好排序对比数据 |
| 格式 | (chosen, rejected) pairs | 偏好/非偏好响应对 |

### RL (PPO) 阶段

| 项目 | 数据量 | 说明 |
|------|--------|------|
| RL Prompts | ~31.7K | PPO 训练使用的 prompts |
| 算法 | PPO + KL 散度约束 | |
| KL penalty (β) | 0.01-0.1 | 防止偏离 SFT 太远 |

### 核心结论

> **1.3B 参数的 InstructGPT 模型的输出，比 175B GPT-3 更受人类偏好**，展示了 RLHF 的强大效果。

---

## 2. DeepSeekMath 7B

**来源**: Shao et al., 2024, "DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models"  
**论文**: [arXiv:2402.03300](https://arxiv.org/abs/2402.03300)  
**代码**: [DeepSeekMath GitHub](https://github.com/deepseek-ai/deepseek-math)

### Pre-training 数据

| 项目 | 数据量 | 说明 |
|------|--------|------|
| Math Tokens | 120B | 从 Common Crawl 提取，使用 fastText 分类器 |
| 数据源 | Common Crawl | 约 40B HTML 去重后 |
| 提取方法 | fastText 分类器 + 人工标注 | 迭代优化 |

### SFT 阶段

| 数据类型 | 描述 | 特点 |
|---------|------|------|
| Chain-of-Thought (CoT) | 数学推理链数据 | 逐步推理过程 |
| Program-of-Thought (PoT) | 代码辅助推理数据 | 代码+数学结合 |
| Tool-Integrated Reasoning | 工具集成推理 | 外部工具调用 |

**训练配置**：

| 参数 | 值 |
|------|-----|
| Batch Size | 10M tokens |
| Learning Rate | 4.2e-4 |
| Max Length | 4K / 8K |

### RL 阶段 (GRPO)

> **GRPO (Group Relative Policy Optimization)** — DeepSeekMath 自研的强化学习算法，是 PPO 的变体。

**核心创新**：省略 critic model，通过组内相对排序估计 baseline，大幅降低训练资源。

| 参数 | 值 | 说明 |
|------|-----|------|
| Learning Rate | 1e-6 | 策略模型学习率 |
| KL Coefficient | 0.04 | KL 散度惩罚系数 |
| 每问题采样数 | 64 | 生成64个输出用于组内比较 |
| Max Length | 1024 | 最大生成长度 |
| Training Batch Size | 1024 | 训练批量大小 |

**Reward Model 训练**：

| 参数 | 值 |
|------|-----|
| 初始化模型 | DeepSeekMath-Base 7B |
| Learning Rate | 2e-5 |

### RL 效果提升

| Benchmark | SFT 后 | GRPO 后 | 提升 |
|-----------|--------|---------|------|
| GSM8K | 82.9% | 88.2% | +5.3% |
| MATH | 46.8% | 51.7% | +4.9% |
| CMATH | 84.6% | 88.8% | +4.2% |

---

## 3. LLaMA 2

**来源**: Touvron et al., 2023, "Llama 2: Open Foundation and Fine-Tuned Chat Models"  
**论文**: [arXiv:2307.09288](https://arxiv.org/abs/2307.09288)

### SFT 阶段

| 项目 | 数据量 | 说明 |
|------|--------|------|
| SFT 数据 | ~2.7M tokens | 高质量标注数据 |
| 质量控制 | 严格筛选 | 人工审核确保质量 |

### RLHF 阶段

| 项目 | 说明 |
|------|------|
| 算法 | PPO + Rejection Sampling |
| 奖励模型 | 人工偏好训练 |
| 具体数据规模 | **未公开** |

---

## 4. Chinchilla (DeepMind)

**来源**: Hoffmann et al., 2022, "Training Compute-Optimal Large Language Models"  
**论文**: [arXiv:2203.15556](https://arxiv.org/abs/2203.15556)

### Pre-training 数据

| 项目 | 数据量 | 说明 |
|------|--------|------|
| Training Tokens | 1.4T | 70B 参数模型 |
| vs Gopher | 4x more data | 同等计算预算下 Gopher 只有 300B tokens |

### 核心发现

> **模型大小和训练 token 数量应该等比例缩放**：每 2x 模型参数，就应该 2x 训练数据。

| 模型 | 参数 | 训练 Tokens | 计算预算 |
|------|------|------------|----------|
| Gopher | 280B | 300B | 1x |
| Chinchilla | 70B | 1.4T | 1x |
| GPT-3 | 175B | 300B | ~5x (估算) |

---

## 5. GLM 系列 (智谱 AI)

> ⚠️ **说明**：GLM-4.5 / GLM-5 作为国内头部模型，官方披露以**能力评测**和**功能特性**为主，**未公开详细 SFT/RL 训练资源数据**（如标注数据量、GPU 数量、具体超参数等）。

### 已公开信息

**SFT 阶段**（通用描述，无具体数字）：
- 高质量人工标注对话数据
- 代码与系统工程任务数据
- 工具调用与结构化输出数据

**RL/对齐阶段**：
- **GLM-4.5**：Targeted fine-tuning + RL 提升 reasoning、coding、agent performance
- **GLM-5**：引入 **Slime** 异步强化学习框架，支持更大模型规模与更复杂 RL 任务
- 重点方向：长程交互学习、多步任务、资源管理、依赖协调

**训练配置**（行业常见量级，无官方数字）：
- Learning Rate：1e-5 ~ 2e-5
- Epochs：1-3
- 批量大小：按模型规模动态调整

### 未公开原因

1. 智谱官方技术文档以**产品能力**为导向，未发布类似 arXiv 论文的详细技术报告
2. GLM-5 作为最新旗舰，训练细节属于竞争敏感信息
3. 国内厂商整体更倾向于**黑盒发布**模式

---

## 6. Qwen 系列 (阿里云通义)

> ⚠️ **说明**：Qwen 2.5 / Qwen 3 / Qwen 3.5 系列中，**Qwen 2.5 有部分公开数据**，Qwen 3 和 Qwen 3.5 **未公开 SFT/RL 详细资源**。

### Qwen 2.5（部分公开）⭐

**来源**: 官方博客 & 技术文档

| 阶段 | 数据集 | 数量 |
|------|--------|------|
| SFT | Qwen-SFT | 100万+ 高质量指令对话 |
| SFT | Open-Instruct | 混合开源指令数据 |
| SFT | Code-Feedback | 50万+ 代码反馈数据 |

**对齐方法**：
- RLHF (奖励模型 + PPO)
- DPO 直接偏好优化

### Qwen 3 / Qwen 3.5（未公开）

**已知的对齐创新方向**：
- 多阶段对齐
- 高质量人类反馈
- 长上下文对齐
- Agent / 工具使用场景优化

**Qwen 3.5 重点对齐场景**：
- 长上下文场景对齐（Qwen3.5-Plus 提供 1M context window）
- Agent / 工具使用场景优化
- 多模态交互对齐（Qwen3.5-397B-A17B 作为 native vision-language model）
- 推理质量优化

### 未公开原因

1. Qwen 3 / Qwen 3.5 技术细节主要通过官方博客发布，**非正式学术论文**
2. 官方博客侧重**产品功能**和** benchmark 性能**，而非训练细节
3. 具体 RL 算法细节（如 GRPO vs PPO）、奖励模型规模、偏好数据量等**属于内部技术**
4. Qwen 3.5 作为 2026 年最新模型，训练资源信息更为敏感

---

## 7. 闭源模型 (未公开具体数据)

| 模型 | SFT 数据 | RL 数据/算法 | 说明 |
|------|---------|-------------|------|
| GPT-4 | **未公开** | **未公开** | 训练成本估算 ~$100M+ |
| Claude 3 | **未公开** | **未公开** | Anthropic 未披露 |
| Gemini Ultra | **未公开** | **未公开** | Google 使用数千 TPU |

**备注**：大多数商业公司（OpenAI、Anthropic、Google）**不公开**具体 SFT/RL 训练资源和数据量，因为这属于商业机密。

---

## 对比总结

### SFT 数据规模对比

| 模型 | SFT 数据量 | 来源 |
|------|-----------|------|
| InstructGPT | ~14.7K demos | 人工标注 |
| DeepSeekMath | CoT+PoT+Tool | 数学专用数据 |
| Qwen 2.5 | 100万+ | 混合（SFT+开源+代码反馈） |
| LLaMA 2 | ~2.7M tokens | 高质量标注 |
| GLM-4.5/5 | 未公开 | 官方未披露 |
| Qwen 3/3.5 | 未公开 | 官方未披露 |

### RL 训练对比

| 模型 | 算法 | 数据量 | 资源消耗 |
|------|------|--------|---------|
| InstructGPT | PPO | 31.7K prompts | 高（需 critic model） |
| DeepSeekMath | GRPO | subset of SFT | 低（无 critic model） |
| LLaMA 2 | PPO + Rejection | 未公开 | 中等 |
| Qwen 2.5 | RLHF + DPO | 未公开 | 中等 |
| GLM-4.5/5 | 异步 RL (Slime) | 未公开 | 高 |
| GPT-4 | **未公开** | **未公开** | 极高 |

### 资源消耗排名（估算）

```
GPT-4 >> GLM-5 (Slime) > InstructGPT (PPO) > Qwen ≈ LLaMA 2 > DeepSeekMath (GRPO)
```

---

## 附录：关键发现

1. **RLHF 效果显著**：1.3B InstructGPT > 175B GPT-3，证明 RLHF 对齐的价值
2. **GRPO 节省资源**：DeepSeekMath 的 GRPO 无需 critic model，显著降低 RL 训练成本
3. **计算最优 scaling**：Chinchilla 证明模型参数和数据量应等比例缩放
4. **商业模型保密**：大多数厂商不公开 RL/SFT 具体资源消耗
5. **国内模型差异化**：GLM/Qwen 等国内模型更倾向黑盒发布，官方披露以能力为主，训练细节较少公开

---

*最后更新：2026-03*
