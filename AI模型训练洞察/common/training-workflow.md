# 训练工作流与流水线平台

## 训练方式对比

### 1. Notebook 模式

**适用场景**：实验探索、原型开发、小规模训练

| 特点 | 说明 |
|------|------|
| **交互式** | 即时反馈，快速迭代 |
| **灵活性** | 自由编写任意代码 |
| **局限性** | 难以规模化、版本化、自动化 |

**主流 Notebook 环境**：
- JupyterLab / JupyterHub
- Google Colab / Colab Enterprise
- Azure ML Notebooks
- SageMaker Notebook

### 2. 流水线平台模式

**适用场景**：规模化训练、生产环境、自动化工作流

| 特点 | 说明 |
|------|------|
| **可复用** | 组件化、模块化 |
| **可版本化** | Pipeline 即代码 |
| **可观测** | 指标追踪、日志、 lineage |
| **可调度** | Cron 触发、事件触发 |

## 主流流水线平台

### 1. Kubeflow Pipelines (KFP)

**Kubernetes 原生 ML 流水线**

| 功能 | 说明 |
|------|------|
| **组件化** | Docker 容器作为执行单元 |
| **DAG** | 有向无环图定义工作流 |
| **SDK** | Python SDK 定义流水线 |
| **存储** | 元数据存储在 MySQL |

**架构**：
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Component  │────▶│  Component  │────▶│  Component  │
│  (Container)│     │  (Container)│     │  (Container)│
└─────────────┘     └─────────────┘     └─────────────┘
        ↓                   ↓                   ↓
   Artifact Store      Artifact Store      Artifact Store
   (MinIO/S3)         (MinIO/S3)           (MinIO/S3)
```

**步骤承接方式**：
- 输出 → 输入通过 Artifact Store 传递
- 参数通过 metrics/parameters 传递
- 支持条件分支

### 2. MLflow Tracking

**实验追踪与模型管理**

| 功能 | 说明 |
|------|------|
| **实验管理** | Run、Experiment、Metric |
| **模型注册** | Model Registry + Versioning |
| **Artifacts** | 文件/模型存储 |
| **Pipeline** | MLflow Pipelines (recipes) |

**典型工作流**：
```python
with mlflow.start_run(run_name="training_v1"):
    # 记录参数
    mlflow.log_params({"lr": 0.001, "batch": 32})
    
    # 记录指标
    mlflow.log_metrics({"loss": 0.5, "accuracy": 0.9})
    
    # 记录模型
    mlflow.pytorch.log_model(model, "model")
    
    # 记录 Artifacts
    mlflow.log_artifact("data/preprocessed.csv")
```

### 3. Airflow (Apache Airflow)

**通用工作流编排**

| 特点 | 说明 |
|------|------|
| **DAG** | Python 定义工作流 |
| **调度** | Cron 表达式、时间间隔 |
| **执行器** | Local, Celery, Kubernetes |
| **生态** | 丰富的 Operators |

**ML 场景示例**：
```python
from airflow import DAG
from airflow.operators.python import PythonOperator

with DAG("ml_training", schedule_interval="@daily") as dag:
    preprocess = PythonOperator(
        task_id="preprocess",
        python_callable=preprocess_data
    )
    
    train = PythonOperator(
        task_id="train",
        python_callable=train_model
    )
    
    evaluate = PythonOperator(
        task_id="evaluate",
        python_callable=evaluate_model
    )
    
    preprocess >> train >> evaluate
```

### 4. Ray + Ray Tune + Ray Train

**分布式计算框架**

| 组件 | 功能 |
|------|------|
| **Ray Core** | 分布式任务调度 |
| **Ray Tune** | 超参数搜索 |
| **Ray Train** | 分布式训练 |
| **Ray Serve** | 模型服务 |

**训练流水线**：
```python
from ray.train import Trainer

trainer = Trainer(num_workers=8, backend="torch")
trainer.start()

# 数据加载 → 训练 → 评估 → 模型保存
trainer.run(train_loop_fn, config={"lr": 0.001})
```

### 5. Weights & Biases (W&B)

**实验追踪平台**

| 功能 | 说明 |
|------|------|
| **可视化** | 指标曲线、对比图 |
| **Artifact** | 模型/数据版本管理 |
| **Reports** | 实验报告生成 |
| **Sweeps** | 超参数搜索 |

## 各公司的训练方式

### OpenAI

| 方面 | 详情 |
|------|------|
| **训练方式** | 自研流水线平台 (非开源) |
| **基础设施** | Azure 集群 + 自研调度系统 |
| **实验追踪** | 内部工具 (类似 MLflow) |
| **特点** | 超大规模、高度自动化 |

**推测的 Pipeline 架构**：
```
数据准备 → 预训练 → SFT → RLHF → 评估 → 上线
    ↓          ↓        ↓      ↓      ↓
 内部数据湖   内部追踪  内部追踪  内部追踪  内部追踪
```

### DeepSeek

| 方面 | 详情 |
|------|------|
| **训练方式** | 自研训练框架 + DeepSpeed |
| **实验追踪** | 可能是 MLflow/W&B 或自研 |
| **部署** | 内部流水线 |
| **特点** | 高效 MoE、注重成本优化 |

### Meta (LLaMA)

| 方面 | 详情 |
|------|------|
| **训练框架** | Megatron-LM |
| **实验追踪** | 可能使用 PyTorch TorchTitan 或自研 |
| **基础设施** | Research SuperCluster (RSC) |
| **特点** | 大规模训练、高效率 |

### MiniMax

| 方面 | 详情 |
|------|------|
| **训练框架** | DeepSpeed |
| **云平台** | 阿里云/火山引擎 |
| **特点** | 紧密结合云原生 |

## 步骤承接的实现方式

### 1. Artifact 传递

**最常见的方式**：通过共享存储传递数据

```
Step 1 输出 ──▶ MinIO/S3 ──▶ Step 2 输入
              ↓
           元数据 (MLflow)
```

**实现示例**：
```python
# Step 1: 保存 Artifact
mlflow.log_artifact("preprocessed_data.pt")

# Step 2: 读取 Artifact  
artifact_uri = run.info.artifact_uri
data = torch.load(f"{artifact_uri}/preprocessed_data.pt")
```

### 2. 参数/指标传递

**通过 Metadata 传递控制信息**：

```python
# Step 1: 输出参数
mlflow.log_params({"best_epoch": 10, "best_loss": 0.3})

# Step 2: 读取参数
prev_run = mlflow.get_run("previous-run-id")
best_epoch = prev_run.data.params["best_epoch"]
```

### 3. Model Registry 传递

**模型级别的传递**：

```python
# Step 1: 注册模型
model_uri = "runs:/run_id/model"
mlflow.register_model(model_uri, "my_model")

# Step 2: 加载模型
model = mlflow.pyfunc.load_model("models:/my_model/production")
```

### 4. 外部存储 (如 Hive/Delta Lake)

**大数据场景**：

```
Spark Job (数据处理) ──▶ Delta Lake ──▶ Spark Job (训练)
                                        ↓
                                   Model Registry
```

### 5. 消息队列 (Kafka/Pulsar)

**事件驱动方式**：

```
Step 1 完成 ──▶ Kafka Topic ──▶ Step 2 触发
    (data:ready)         (training:start)
```

## 构建 AI 生产线的关键要素

### 1. 基础设施层

| 组件 | 选择 |
|------|------|
| **计算** | Kubernetes + GPU Operator |
| **存储** | MinIO (开发) / S3/OSS (生产) |
| **网络** | RDMA (大规模训练) |

### 2. 编排层

| 组件 | 选择 |
|------|------|
| **任务编排** | Airflow / Prefect / Dagster |
| **ML Pipeline** | Kubeflow / MLflow Pipeline |
| **调度** | Cron / 事件触发 |

### 3. 实验追踪层

| 组件 | 选择 |
|------|------|
| **Tracking** | MLflow / W&B |
| **Metadata** | MLflow Registry / TensorBoard |
| **可视化** | TensorBoard / W&B Reports |

### 4. 模型服务层

| 组件 | 选择 |
|------|------|
| **Serving** | Triton / KServe / vLLM |
| **监控** | Prometheus + Grafana |
| **A/B Testing** | 路由层控制 |

### 5. 数据/特征层

| 组件 | 选择 |
|------|------|
| **Feature Store** | Feast / Redis / RedisAI |
| **Data Versioning** | DVC / LakeFS |
| **Data Validation** | Great Expectations / Pydantic |

## 典型 AI 生产线架构

```
┌─────────────────────────────────────────────────────────────────┐
│                        数据层                                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │ 原始数据  │  │ 数据清洗  │  │ 特征工程  │  │ 特征存储  │       │
│  │ (Object) │─▶│ (Spark)  │─▶│ (Spark)  │─▶│ (Feast)  │       │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                        训练层                                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │ 数据加载  │  │ 模型训练  │  │ 模型评估  │  │ 模型注册  │       │
│  │ (DataLoader)│▶│ (Trainer)│▶│ (Eval)  │─▶│ (Registry)│      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
│        ↓            ↓            ↓            ↓              │
│   MLflow Tracking / Weights & Biases                           │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                        服务层                                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │ 模型部署  │  │ 推理服务  │  │ 监控告警  │  │ A/B 测试 │       │
│  │ (Triton) │  │ (API)    │  │ (Prom)  │  │ (Router)│       │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
└─────────────────────────────────────────────────────────────────┘
```

---

*最后更新：2026-03-24*