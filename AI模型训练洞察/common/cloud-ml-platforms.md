# 云平台 ML 训练平台技术栈分析

> 构建 AI 训练平台软件层的技术选型参考

---

## 一、云平台 ML 服务概览

| 云厂商 | ML 平台 | 定位 | 主要特点 |
|--------|---------|------|----------|
| **AWS** | SageMaker | 全栈 ML 平台 | 企业级、功能最全、生态成熟 |
| **GCP** | Vertex AI | 统一 ML 平台 | AutoML 强、开源友好、MLOps 原生 |
| **Azure** | Azure ML | 企业 ML 平台 | 与微软生态深度集成、Copilot 集成 |
| **阿里云** | PAI (机器学习PAI) | 阿里 ML 平台 | Alink 算法库、本地化支持 |
| **火山引擎** | VEBA | 字节 ML 平台 | 短视频/推荐场景优势 |

---

## 二、各平台软件架构详解

### 2.1 AWS SageMaker

**核心组件矩阵**：

| 组件 | 功能 | 技术实现 |
|------|------|----------|
| **SageMaker Studio** | Web IDE/Notebook 环境 | 基于 Jupyter |
| **Training Jobs** | 托管训练任务 | 容器化 + 弹性伸缩 |
| **Processing Jobs** | 数据预处理 | Spark/Dask 容器 |
| **Model Registry** | 模型版本管理 | 版本控制 + 打包 |
| **Feature Store** | 特征存储 | 离线 + 在线双存储 |
| **Pipeline** | 流水线编排 | DAG + Step Functions |
| **Endpoint** | 模型服务 | Real-time / Serverless |
| **Edge Manager** | 边缘部署 | 设备管理 |

**技术栈亮点**：

```python
# SageMaker Python SDK V3 示例
from sagemaker import ModelTrainer, ModelBuilder

# 训练
trainer = ModelTrainer(
    role="arn:aws:iam::123456789:role/sagemaker-role",
    instance_count=8,
    instance_type="ml.p4d.24xlarge"
)
trainer.fit("s3://bucket/data")

# 部署
builder = ModelBuilder(
    framework="pytorch",
    model_type="causal-lm"
)
predictor = builder.deploy(
    instance_type="ml.g5.xlarge",
    initial_instance_count=2
)
```

**核心能力**：
- **训练模式**：内置算法 / 框架容器 / 自定义容器
- **超参调优**：Automatic Model Tuning (Bayesian)
- **分布式训练**：SageMaker Distributed (数据/模型并行)
- **MLOps**：Model Monitor + Clarify (漂移检测 + 偏见检测)

---

### 2.2 GCP Vertex AI

**核心组件矩阵**：

| 组件 | 功能 | 技术实现 |
|------|------|----------|
| **Vertex AI Workbench** | Notebook 环境 | 基于 JupyterLab |
| **Custom Training** | 自定义训练 | Vertex Training Service |
| **Vertex Pipelines** | 流水线编排 | Kubeflow Pipelines |
| **Vertex Feature Store** | 特征存储 | BigTable + Redis |
| **Vertex Model Registry** | 模型管理 | 元数据存储 |
| **Vertex Endpoints** | 在线服务 | HTTP 端点 + 批量预测 |
| **Vertex AI Search** | RAG/语义搜索 | 向量检索 |
| **Prediction** | 预测服务 | Container / Built-in |

**技术栈亮点**：

```python
# Vertex AI Python SDK
from google.cloud import aiplatform

# 启动训练
job = aiplatform.CustomTrainingJob(
    display_name="my-training-job",
    container_uri="gcr.io/my-project/trainer:latest",
    model_display_name="my-model"
)
job.run(
    replica_count=8,
    machine_type="n1-standard-16",
    accelerator_type="NVIDIA_TESLA_V100",
    accelerator_count=4
)

# 部署
model = aiplatform.Model.upload(
    container_uri="gcr.io/my-project/serving:latest"
)
endpoint = model.deploy(machine_type="n1-standard-4")
```

**核心能力**：
- **AutoML**：Tables / Text / Image / Video / Tabular
- **Vertex Pipelines**：KFP + TFX (TensorFlow Extended) 集成
- **Feature Store**：离线/在线一体、特征共享
- **TensorBoard**：深度学习可视化 (原生集成)
- **Model Garden**：预训练模型库 (TensorFlow/PyTorch/JAX)

**与开源生态**：
- PyTorch / TensorFlow / JAX 原生支持
- Kubeflow 原生集成
- TFX Pipeline 兼容

---

### 2.3 Azure ML

**核心组件矩阵**：

| 组件 | 功能 | 技术实现 |
|------|------|----------|
| **Azure ML Studio** | Web IDE | 浏览器端 ML 工作台 |
| **Compute Instances** | 计算实例 | 托管 Jupyter |
| **Training Clusters** | 训练集群 | 弹性 GPU 集群 |
| **MLflow Integration** | 实验追踪 | MLflow Tracking Server |
| **Azure Pipelines** | CI/CD | Azure DevOps 集成 |
| **Azure Kubernetes** | 推理集群 | AKS 集成 |
| **Azure AI Studio** | 生成式 AI | Copilot Studio 集成 |

**技术栈亮点**：

```python
# Azure ML Python SDK
from azure.ai.ml import MLClient
from azure.ai.ml import command, output

# 定义训练
job = command(
    code="./src",
    command="python train.py",
    environment="azureml:my-env:1",
    compute="gpu-cluster",
    instance_count=8
)

# 提交
returned_job = ml_client.jobs.create(job)

# 流水线
from azure.ai.ml.dsl import pipeline

@pipeline
def ml_pipeline():
    preprocess = command(...)
    train = command(...)
    evaluate = command(...)
    
    preprocess.outputs.data >> train.inputs.data
    train.outputs.model >> evaluate.inputs.model
```

**核心能力**：
- **Designer**：拖拽式可视化建模
- **AutoML**：分类/回归/时序预测
- **Responsible AI**：可解释性、偏见检测
- **MLOps**：Azure DevOps 集成、GitHub Actions
- **Azure AI Studio**：生成式 AI、RAG、Copilot

---

### 2.4 阿里云 PAI

**核心组件矩阵**：

| 组件 | 功能 | 技术实现 |
|------|------|----------|
| **PAI Studio** | Web IDE | 可视化建模 + Notebook |
| **DSW (IDE)** | 交互式建模 | JupyterLab 风格 |
| **EAS** | 在线服务 | 弹性推理服务 |
| **Feature Store** | 特征平台 | 离线/在线特征 |
| **Alink** | 算法库 | Flink ML 算法 |
| **AutoML** | 自动调参 | 超参搜索 |
| **PAI-Blade** | 模型优化 | 剪枝/量化/蒸馏 |
| **EasyAI** | 预置模型 | 视觉/NLP/推荐模型 |

**技术栈亮点**：

```python
# PAI Python SDK
from pai.session import get_default_session

# 创建训练任务
session = get_default_session()

# 使用 TensorFlow/PyTorch 框架训练
estimator = session.run(
    algorithm='tensorflow',
    hyper_parameters={
        'batch_size': 32,
        'learning_rate': 0.001
    },
    input_config='oss://bucket/data',
    output_config='oss://bucket/output'
)

# 部署为在线服务
service = session.deploy(
    model_path='oss://bucket/model',
    instance_type='ecs.g6'
)
```

**核心能力**：
- **Alink**：Flink 生态的 ML 算法库 (批流一体)
- **PAI-Blade**：深度学习模型优化 (推理加速)
- **预置模型**：开箱即用的 CV/NLP/推荐模型
- **本地化**：中文 NLP/语音/视觉模型优化

---

## 三、自研平台的组件选型建议

### 3.1 核心组件矩阵

基于上述分析，推荐的自研 AI 训练平台组件：

| 功能层 | 推荐组件 | 选型理由 | 替代方案 |
|--------|----------|----------|----------|
| **Web IDE** | JupyterHub / VS Code Server | 成熟、社区活跃 | CloudLab, Zeppelin |
| **训练任务管理** | K8s Operator + Volcano | K8s 原生、调度优化 | Ray, Airflow |
| **实验追踪** | MLflow | 开源、功能完整 | W&B, TensorBoard |
| **模型管理** | MLflow Registry | 与实验追踪一体 | DVC, ModelDB |
| **特征存储** | Feast | 开源、活跃 | Redis, Hive |
| **流水线编排** | Kubeflow Pipelines / Airflow | 行业标准 | Prefect, Dagster |
| **数据版本控制** | DVC / LakeFS | Git 风格、易上手 | Git LFS, Delta Lake |
| **任务调度** | Airflow / Prefect | 丰富 Operators | Argo Workflows |
| **监控告警** | Prometheus + Grafana | K8s 生态标配 | DataDog, CloudWatch |
| **模型服务** | Triton / KServe | 性能优化、支持多框架 | vLLM, FastAPI |
| **权限管理** | Keycloak / OAuth2 | 企业级 SSO | LDAP, SAML |

### 3.2 技术栈选型决策树

```
┌─────────────────────────────────────────────────────────────────┐
│                        需求场景                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ↓                    ↓                    ↓
   ┌─────────────┐      ┌─────────────┐      ┌─────────────┐
   │  起步阶段    │      │  成长阶段    │      │  成熟阶段    │
   │  (小团队)   │      │  (中团队)   │      │  (大团队)   │
   └─────────────┘      └─────────────┘      └─────────────┘
         ↓                    ↓                    ↓
   ┌─────────────┐      ┌─────────────┐      ┌─────────────┐
   │ JupyterHub  │      │ Kubeflow    │      │ 全栈自研    │
   │ + MLflow   │      │ + Airflow   │      │ + 定制化    │
   │ + MinIO    │      │ + Feast     │      │ + 多租户    │
   └─────────────┘      └─────────────┘      └─────────────┘
```

### 3.3 技术栈组合推荐

#### 💰 经济型 (预算有限，小规模)

| 组件 | 技术选型 | 部署复杂度 |
|------|----------|----------|
| 计算 | K3s + GPU Operator | ⭐ |
| 训练 | PyTorch + DDP | ⭐ |
| 实验追踪 | MLflow (Local) | ⭐ |
| 存储 | MinIO + NFS | ⭐⭐ |
| 模型服务 | FastAPI + Triton | ⭐⭐ |

#### ⚖️ 平衡型 (中等规模，需要生产级)

| 组件 | 技术选型 | 部署复杂度 |
|------|----------|----------|
| 计算 | K8s + Volcano | ⭐⭐ |
| 训练 | Kubeflow Training | ⭐⭐ |
| 实验追踪 | MLflow (Remote) + GFS | ⭐⭐⭐ |
| 特征存储 | Feast | ⭐⭐⭐ |
| 流水线 | Kubeflow Pipelines | ⭐⭐⭐ |
| 模型服务 | Triton + KServe | ⭐⭐⭐ |

#### 🚀 企业级 (大规模，多租户)

| 组件 | 技术选型 | 部署复杂度 |
|------|----------|----------|
| 计算 | K8s + RDMA + GDR | ⭐⭐⭐⭐ |
| 训练 | 定制 K8s Operator | ⭐⭐⭐⭐⭐ |
| 实验追踪 | MLflow 集群 + MySQL | ⭐⭐⭐⭐ |
| 特征存储 | 自研 Feature Store | ⭐⭐⭐⭐⭐ |
| 流水线 | Airflow + 自研组件 | ⭐⭐⭐⭐ |
| 模型服务 | 自研推理平台 | ⭐⭐⭐⭐⭐ |
| 多租户 | 自研 SaaS 层 | ⭐⭐⭐⭐⭐ |

---

## 四、关键实现功能详解

### 4.1 训练任务生命周期管理

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  创建任务  │───▶│  调度    │───▶│  执行    │───▶│  监控    │───▶│  完成    │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │              │              │              │
     ▼              ▼              ▼              ▼              ▼
  参数配置      资源分配      容器运行      日志/指标      模型保存
  代码上传      节点选择      环境配置      GPU 利用率     记录元数据
  数据挂载      优先级        GPU 分配      Checkpoint     回调通知
```

**核心实现**：
- **任务定义**：YAML/JSON + Web UI
- **调度器**：FIFO / Priority / Gang Scheduling
- **资源隔离**：Namespace + ResourceQuota
- **状态追踪**：CRD + Operator 模式

### 4.2 多租户与权限控制

```
┌─────────────────────────────────────────────────────────────┐
│                     多租户架构                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Tenant A          Tenant B          Tenant C            │
│   ┌─────────┐      ┌─────────┐      ┌─────────┐          │
│   │ Namespace│      │ Namespace│      │ Namespace│          │
│   │ Quota   │      │ Quota   │      │ Quota   │          │
│   │ RBAC    │      │ RBAC    │      │ RBAC    │          │
│   └─────────┘      └─────────┘      └─────────┘          │
│        ↓                ↓                ↓                 │
│   ┌─────────────────────────────────────────────┐         │
│   │           共享基础设施 (K8s)                  │         │
│   │  GPU 集群 | 存储 | 网络 | 监控系统            │         │
│   └─────────────────────────────────────────────┘         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**权限模型**：
- **RBAC**：Role → RoleBinding → User/Group
- **租户配额**：CPU/GPU/内存/存储限额
- **资源隔离**：网络策略 + 存储卷隔离

### 4.3 数据与模型版本管理

```
┌────────────────────────────────────────────────────────────────┐
│                     版本控制流程                               │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  Data ──▶ DVC ──▶ S3/MinIO ──▶ Data Version                   │
│                        ↓                                      │
│  Code ──▶ Git ──▶ Git Server ──▶ Code Version                 │
│                        ↓                                      │
│  Model ──▶ MLflow ──▶ Model Registry ──▶ Model Version       │
│                        ↓                                      │
│  Pipeline ──▶ YAML ──▶ Git ──▶ Pipeline Version              │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### 4.4 流水线步骤衔接

| 方式 | 实现 | 适用场景 |
|------|------|----------|
| **Artifact 存储** | MinIO/S3 文件传递 | 大数据/模型文件 |
| **数据库** | MySQL/PostgreSQL 状态传递 | 小数据/参数 |
| **消息队列** | Kafka/RabbitMQ 事件触发 | 异步解耦 |
| **API 调用** | HTTP REST | 微服务间调用 |
| **共享存储** | PVC / NFS | 容器间共享 |

---

## 五、技术选型建议

### 5.1 选型原则

1. **先开源后自研**：优先使用成熟开源组件，避免重复造轮子
2. **云原生优先**：基于 K8s 生态，降低运维成本
3. **渐进式演进**：从简单开始，逐步添加复杂功能
4. **团队能力匹配**：根据团队技术栈选型，避免过度复杂

### 5.2 路线图建议

| 阶段 | 周期 | 目标 | 关键交付 |
|------|------|------|----------|
| **MVP** | 1-2 月 | Notebook + 训练任务 | JupyterHub + K8s Training |
| **V1.0** | 2-3 月 | 实验追踪 + 模型管理 | MLflow + MinIO |
| **V1.5** | 2-3 月 | 流水线 + 特征存储 | Kubeflow Pipelines + Feast |
| **V2.0** | 3-6 月 | 模型服务 + MLOps | Triton + 监控告警 |
| **V2.5** | 6-12 月 | 多租户 + 企业级功能 | SaaS 层 + 权限系统 |

---

## 六、参考架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                        用户层                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   Web UI     │  │  JupyterLab  │  │   API (REST) │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                        接入层 (API Gateway)                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   认证鉴权    │  │    限流     │  │   路由分发    │        │
│  │ (Keycloak)  │  │             │  │             │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                        服务层                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   任务管理    │  │   实验追踪    │  │   模型管理    │        │
│  │ (K8s Operator)│ │   (MLflow)   │  │ (MLflow Reg) │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │  特征存储     │  │   流水线     │  │   调度器     │        │
│  │   (Feast)   │  │ (KFP/Airflow) │  │ (Airflow)   │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                        基础设施层                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   K8s 集群    │  │   GPU 资源   │  │    存储      │        │
│  │ (计算/网络)  │  │ (GPU Operator)│  │ (MinIO/S3)  │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
│  ┌──────────────┐  ┌──────────────┐                          │
│  │  监控 (Prom) │  │  日志 (ELK)   │                          │
│  └──────────────┘  └──────────────┘                          │
└─────────────────────────────────────────────────────────────────┘
```

---

*最后更新：2026-03-24*