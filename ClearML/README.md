# ClearML — Technical Overview

## 1. What is ClearML?
ClearML is an **open-source MLOps platform** that provides:
- Experiment tracking
- Dataset versioning
- Pipeline orchestration
- Model registry & deployment tools

Works with **any ML/DL framework** (PyTorch, TensorFlow, HuggingFace, etc.).

## 2. Architecture Diagram
     +---------------------+
     |   Your Code / ML    |
     |   Notebooks/Scripts |
     +----------+----------+
                |
                v
         [ClearML SDK]
                |
                v
     +----------+-----------+
     |     ClearML Server   |
     |----------------------|
     |  API Server (Flask)  |
     |  MongoDB (Metadata)  |
     |  Elasticsearch (Idx) |
     |  File Storage (S3,   |
     |  MinIO, NFS, etc.)   |
     +----------+-----------+
                |
     +----------+-----------+
     | ClearML Web UI / API |
     +----------+-----------+
                |
     +----------+-----------+
     | ClearML Agents       |
     | (Remote Executors)   |
     | GPU / CPU / Cloud    |
     +----------------------+
     
## 3. Core Components

| Component          | Description |
|--------------------|-------------|
| **ClearML SDK**    | Python package to log experiments, metrics, artifacts, datasets. |
| **ClearML Server** | Central backend (can be self-hosted or SaaS) that stores metadata, artifacts, and provides the Web UI. |
| **ClearML Agents** | Workers that pull jobs from queues and execute them in replicated environments. |
| **Web UI**         | Interface for viewing experiments, datasets, pipelines, and models. |

---
## 4. Data Flow

1. **Run Script with SDK**
   - Tracks hyperparameters, metrics, plots, and artifacts automatically.
   - Sends them to the **ClearML Server**.

2. **Server Storage**
   - **MongoDB** → Experiment metadata & logs.
   - **Elasticsearch** → Fast search & filtering.
   - **File Storage (S3, GCP, MinIO, etc.)** → Artifacts & datasets.

3. **Remote Execution**
   - Enqueue a task → Agent pulls it → Sets up environment → Runs the job.
   - Ensures reproducibility (same code, data, env).

---

## 5. Key Features

- **Experiment Tracking** — Automatic logging of metrics, code, and environment.
- **Dataset Management** — CLI/Python API for dataset versioning.
- **Pipeline Orchestration** — DAG-based pipelines with step dependencies.
- **Model Registry** — Version-controlled storage of trained models.
- **Remote Execution** — Run tasks on remote agents without changing your code.

---

## 6. Example: Basic Experiment Tracking

```python
from clearml import Task

# Initialize experiment
task = Task.init(project_name="Image Classification",
                 task_name="ResNet50_Training")

# Log scalar metric
logger = task.get_logger()
logger.report_scalar("loss", "train", iteration=1, value=0.25)
```

## 7. Deployment Modes

- **Local Dev Mode** — SaaS server or local instance.
- **Team Mode** — Self-hosted server + agents.
- **Enterprise Mode** — Kubernetes deployment, autoscaling GPU agents, SSO integration.

---

## 8. Integrations

- **ML Frameworks:** PyTorch, TensorFlow, Scikit-learn, XGBoost, HuggingFace, LightGBM.
- **Data Storage:** S3, GCP, Azure Blob, MinIO, NFS.
- **CI/CD:** Jenkins, GitHub Actions, GitLab CI/CD.
- **Deployment:** TorchServe, TF Serving, custom APIs.

