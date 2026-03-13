# 主流大模型训练横向对比

> 说明：以下内容基于公开论文、技术报告、官方博客与行业整理。不同模型的公开程度差异很大，部分信息为公开推断，不宜视为厂商确认事实。

## 一览表

| 模型系列 | 代表版本 | 架构倾向 | 预训练数据特点 | 后训练/对齐特点 | 长上下文特点 | 公开程度 |
|----------|----------|----------|----------------|-----------------|--------------|----------|
| GPT | GPT-3 / GPT-4 / GPT-4o | Decoder-only，部分版本常被推测含 MoE | 网页、书籍、代码、高质量混合语料 | SFT + RLHF，近年也常讨论 DPO 等替代方案 | 中长上下文逐代提升 | 中等偏低 |
| LLaMA | LLaMA 2 / 3 / 3.1 | Dense Transformer 为主 | 网页、代码、数学/科学比例较高 | SFT + RLHF / DPO | 从 4K 演进到 128K | 较高 |
| Claude | Claude 2 / 3 / 4 | Decoder-only，系统设计强调安全与对齐 | 更强调高质量过滤、安全过滤与人工审核 | RLHF + RLAIF + Constitutional AI | 100K~200K+ | 中等 |
| DeepSeek | V2 / V3 / Coder / Math | MoE 特征明显 | 中英双语、代码、数学数据较突出 | SFT + GRPO / RLHF | 32K~128K | 较高 |
| GLM | GLM-4 / GLM-5 | 自回归空白填充目标 + Transformer | 通用文本、代码、学术混合 | SFT + RLHF | 128K | 中等 |
| Qwen | Qwen 2 / 2.5 / 3 | Dense 到 MoE 并存 | 多语言、代码、数学数据较强 | SFT + RLHF / DPO / 多阶段对齐 | 32K~128K+ | 较高 |
| MiniMax | abab / M2 / M2.5 | Dense 与超大规模架构并存 | 中英网页、代码、图书、学术混合 | SFT + RLHF / DPO / GRPO | 128K 到超长上下文 | 中等偏低 |
| 闭源模型（Grok/Gemini） | Grok / Gemini | 多为推测，Gemini 更强调原生多模态 | 大规模私有+公开混合数据 | 强依赖内部对齐与安全体系 | 极长上下文明显 | 低 |

## 按几个关键维度看差异

### 1. 数据策略
- **GPT / Claude**：更强调高质量数据筛选与体系化后训练，公开细节相对克制。
- **LLaMA / Qwen / DeepSeek**：公开资料相对更多，便于从论文和模型卡中拼出训练路径。
- **DeepSeek / Qwen**：在代码、数学、中文与多语言方向上投入感更明显。
- **Gemini**：多模态数据的重要性远高于一般纯文本模型。

### 2. 架构趋势
- **LLaMA / Qwen 2 / GLM**：更偏经典 Dense Transformer 路线。
- **DeepSeek V2/V3、Qwen 3、部分 GPT 版本推测**：MoE 成为降低激活成本、提升容量的重要方向。
- **Claude / Gemini**：公开重点更多在系统能力、长上下文、工具使用和安全，而不是完整结构细节。

### 3. 对齐方法
- **GPT**：RLHF 是公开叙事中的代表路线。
- **Claude**：Constitutional AI + RLAIF 是最有辨识度的路线。
- **LLaMA / Qwen / DeepSeek**：越来越多采用 DPO、GRPO 等更稳定或更高效的方法。

### 4. 长上下文能力
- **Claude / Gemini**：长上下文宣传最强，属于产品差异化重点。
- **Qwen / GLM / DeepSeek / MiniMax**：也在快速追赶 128K 甚至更高上下文。
- **LLaMA**：随着版本演进，长上下文能力逐步增强，但重点仍常放在开源生态影响力。

### 5. 资料可信度边界
- **开源模型/开放报告较多的模型**：如 LLaMA、Qwen、DeepSeek，很多结论更容易追溯。
- **闭源模型**：很多数字、参数量、数据配比只能视为行业估计，引用时要特别注明“推测”或“公开分析”。

## 阅读建议

如果你的目标是：

- **学标准训练流程**：优先看 GPT、LLaMA
- **学对齐与安全体系**：优先看 Claude
- **学 MoE 与效率优化**：优先看 DeepSeek、Qwen 3
- **学中文/多语言落地**：优先看 Qwen、GLM、MiniMax
- **学长上下文与多模态产品方向**：优先看 Gemini、Claude

## 建议后续补充方向

1. 增加“版本发布时间线”页面
2. 增加“公开论文 / 技术报告索引”页面
3. 为每个模型补充“信息公开边界”说明
4. 增加“训练数据、架构、对齐方法”三张对比表

## 参考资料

- GPT-3: https://arxiv.org/abs/2005.14165
- GPT-4: https://arxiv.org/abs/2303.08774
- InstructGPT: https://arxiv.org/abs/2203.02155
- LLaMA: https://arxiv.org/abs/2302.13971
- LLaMA 2: https://arxiv.org/abs/2307.09288
- Llama 3（Meta News/Blog）: https://ai.meta.com/blog/meta-llama-3/
- Claude / Constitutional AI: https://arxiv.org/abs/2212.08073
- DeepSeek-V2: https://arxiv.org/abs/2405.04434
- DeepSeek-V3: https://github.com/deepseek-ai/DeepSeek-V3
- GLM-130B: https://arxiv.org/abs/2210.05858
- Qwen: https://arxiv.org/abs/2309.16609
- Qwen 2: https://arxiv.org/abs/2406.06187
- Gemini: https://arxiv.org/abs/2312.11805
