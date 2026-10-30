# ML Production Pipeline Capstone

> An end-to-end MLOps / Machine Learning production pipeline demonstrating how a machine learning project moves from data ingestion and preprocessing to model training, evaluation, experiment tracking, versioning, API serving, containerization, CI/CD, cloud deployment, and monitoring.

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [DVC Pipeline](#dvc-pipeline)
- [Experiment Tracking](#experiment-tracking)
- [FastAPI Inference Service](#fastapi-inference-service)
- [Docker](#docker)
- [AWS Deployment](#aws-deployment)
- [Kubernetes / Amazon EKS](#kubernetes--amazon-eks)
- [CI/CD](#cicd)
- [Monitoring](#monitoring)
- [Testing](#testing)
- [Local Setup](#local-setup)
- [Environment Variables & Secrets](#environment-variables--secrets)
- [Reproducibility](#reproducibility)
- [Future Improvements](#future-improvements)
- [Security](#security)
- [License](#license)
- [Authors](#authors)

---

## Overview

This project implements a complete ML lifecycle as a production-oriented system rather than a notebook-only workflow:

- Data ingestion and preprocessing
- Feature engineering
- Model training and evaluation
- MLflow experiment tracking
- DVC data and pipeline versioning
- Model registration/promotion
- FastAPI inference service
- Docker containerization
- GitHub Actions CI/CD
- AWS ECR image management
- Amazon EKS deployment
- Kubernetes LoadBalancer service
- Prometheus monitoring
- Grafana dashboards
- Automated testing

The repository follows a modular structure under `src/` and uses DVC stages to make the ML workflow reproducible.

---

## Architecture

```text
Raw Dataset
    │
    ▼
Data Ingestion
    │
    ▼
Data Preprocessing
    │
    ▼
Feature Engineering
    │
    ▼
Model Building
    │
    ▼
Model Evaluation
    │
    ▼
Model Registration
    │
    ├──────────┬──────────┐
    ▼          ▼          ▼
FastAPI API  Model      MLflow
             Registry
    │
    ▼
Docker Image
    │
    ▼
AWS ECR
    │
    ▼
Amazon EKS
    │
    ▼
LoadBalancer
    │
    ├─────────┬─────────┐
    ▼         ▼         ▼
Prometheus Grafana ...
Monitoring  Dashboard
```

---

## DVC Pipeline

The DVC pipeline defines the following stages:

| Stage | Description |
|-------|-------------|
| Data Ingestion | Loads raw data and splits into train/test |
| Data Preprocessing | Cleans and preprocesses text data |
| Feature Engineering | Applies CountVectorizer (Bag of Words) |
| Model Building | Trains Logistic Regression model |
| Model Evaluation | Evaluates model and logs metrics |
| Model Registration | Registers model to MLflow Model Registry |

**Configured parameters** (`params.yaml`):

```yaml
data_ingestion:
  test_size: 0.25

feature_engineering:
  max_features: 50
```

**Reproduce the pipeline:**

```bash
dvc repro
```

**Check pipeline status:**

```bash
dvc status
```

**Push tracked data/artifacts to the configured remote:**

```bash
dvc push
```

The repository also contains: `.dvc/`, `dvc.yaml`, `dvc.lock`, `params.yaml`.

---

## Experiment Tracking

The project integrates MLflow and DagsHub for experiment tracking and artifact management.

- Experiment tracking
- Run metadata
- Model/artifact logging
- Experiment information
- Model registration/promotion

Configure your own DagsHub/MLflow credentials through environment variables or your CI/CD secret store. Never commit access tokens or credentials to the repository.

---

## FastAPI Inference Service

The trained model is exposed through a FastAPI application.

The Docker configuration runs the API on `0.0.0.0:5000`.

**Start the application locally:**

```bash
uvicorn app:app --host 0.0.0.0 --port 5000
```

The API can then be accessed at: `http://localhost:5000`

---

## Docker

The project includes a production-oriented Dockerfile.

**Build image:**

```bash
docker build -t capstone-app:latest .
```

**Run container:**

```bash
docker run -p 8888:5000 capstone-app:latest
```

**Run with secret environment variable:**

```bash
docker run -p 8888:5000 -e CAPSTONE_TEST=<YOUR_SECRET> capstone-app:latest
```

> ⚠️ Never place real credentials directly in commands that may be stored in shell history or documentation.

---

## AWS Deployment

The project documents a cloud deployment workflow using AWS services:

- Amazon S3
- Amazon ECR
- Amazon EKS
- Amazon EC2
- AWS IAM
- AWS CloudFormation

The CI/CD workflow builds and pushes the Docker image to Amazon ECR before deployment.

### Amazon ECR

The container image is designed to be pushed to an Amazon ECR repository.

Typical workflow:

```text
Code → Docker Build → Docker Image → Amazon ECR → Amazon EKS
```

Required AWS configuration includes credentials, region, account ID, and ECR repository settings. Use GitHub Actions Secrets/Variables for these values instead of committing credentials.

---

## Kubernetes / Amazon EKS

The repository contains `deployment.yaml` for Kubernetes deployment.

**Configured deployment:**

- Runs 2 FastAPI replicas
- Uses a Kubernetes Deployment
- Exposes container port 5000
- Uses CPU and memory requests/limits
- Loads a secret through Kubernetes SecretKeyRef
- Creates a LoadBalancer service

**Useful commands:**

```bash
kubectl get nodes
kubectl get pods
kubectl get svc
```

**Apply the deployment:**

```bash
kubectl apply -f deployment.yaml
```

---

## CI/CD

The project contains GitHub Actions workflows under `.github/workflows/`.

The documented CI/CD flow covers:

```text
Git Push → Tests / Validation → Docker Build → Push Image to ECR → Deploy to EKS
```

This makes the project suitable for demonstrating automated ML application delivery.

---

## Monitoring

The project includes a monitoring setup using Prometheus and Grafana.

- **Prometheus**: Configured to scrape the deployed application (`scrape_interval: 15s`)
- **Grafana**: Used to visualize Prometheus metrics through dashboards

```text
Prometheus → Grafana
```

This provides an observability layer around the deployed ML API.

---

## Testing

The repository includes:

- `tests/`
- `test.py`
- `test_environment.py`
- `tox.ini`

Pytest is included in the project's dependencies.

**Run tests with:**

```bash
pytest
```

---

## Project Structure

```
ML_Production_pipeline_capston_pro/
├── .dvc/
├── .github/
│   └── workflows/
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
├── docs/
├── fastapi_app/
│   └── templates/
├── mlartifacts/
├── models/
├── notebooks/
├── references/
├── reports/
│   └── figures/
├── script/
│   └── promote_model.py
├── src/
│   ├── connections/
│   ├── data/
│   ├── features/
│   ├── logger/
│   ├── models/
│   └── visualization/
├── tests/
├── Dockerfile
├── deployment.yaml
├── dvc.yaml
├── dvc.lock
├── params.yaml
├── projectflow.txt
├── requirements.txt
├── setup.py
├── test.py
├── test_environment.py
└── tox.ini
```

---

## Tech Stack

| Category | Technologies |
|----------|-------------|
| **Machine Learning** | Python, NumPy, Pandas, Scikit-learn, NLTK, Joblib |
| **MLOps** | MLflow, DVC, DagsHub, Model Registry, Experiment Tracking |
| **API** | FastAPI, Uvicorn, Pydantic |
| **DevOps** | Docker, GitHub Actions, CI/CD, Pytest, Tox |
| **Cloud & Infrastructure** | AWS S3, AWS ECR, Amazon EKS, Amazon EC2, AWS IAM, AWS CloudFormation, Kubernetes, kubectl, eksctl |
| **Monitoring** | Prometheus, Grafana |

---

## Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/ML_Production_pipeline_capston_pro.git
cd ML_Production_pipeline_capston_pro
```

### 2. Create a virtual environment

```bash
conda create -n atlas python=3.10
conda activate atlas
```

Or use a standard Python virtual environment:

```bash
python -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Install the project

```bash
pip install -e .
```

### 5. Run the DVC pipeline

```bash
dvc repro
```

### 6. Check generated artifacts

```text
data/
models/
reports/
```

---

## Environment Variables & Secrets

The deployment workflow uses secrets for application and cloud configuration.

| Variable | Description |
|----------|-------------|
| `CAPSTONE_TEST` | Application secret token |
| `AWS_ACCESS_KEY_ID` | AWS access key ID |
| `AWS_SECRET_ACCESS_KEY` | AWS secret access key |
| `AWS_REGION` | AWS region |
| `AWS_ACCOUNT_ID` | AWS account ID |
| `ECR_REPOSITORY` | ECR repository name |

For production:

- Store secrets in GitHub Actions Secrets
- Use Kubernetes Secrets for cluster workloads
- Use AWS IAM roles where appropriate
- Do not commit credentials, API tokens, or private keys

---

## Reproducibility

One of the major goals of this project is reproducibility.

The combination of:

- DVC
- `params.yaml`
- `dvc.lock`
- MLflow
- version-controlled source code

allows data, pipeline stages, parameters, and experiments to be tracked systematically.

---

## Future Improvements

Potential improvements include:

- Add automated data validation
- Add model drift detection
- Add automated model retraining
- Add model performance monitoring
- Add a formal model registry workflow
- Add security scanning to CI/CD
- Add Kubernetes Horizontal Pod Autoscaling
- Add centralized logging
- Add automated integration tests
- Add infrastructure-as-code with Terraform
- Add canary or blue-green deployments

---

## Security

The repository's project-flow documentation contains historical setup notes involving credentials/secrets. Do not reuse or publish any credential shown in historical documentation. Rotate any credential that may have been exposed and use environment variables, GitHub Secrets, Kubernetes Secrets, or AWS IAM instead.

---

## License

This project is licensed under the MIT License.

---

## Authors

This project was originally developed as an ML Production Pipeline capstone.

GitHub: https://github.com/YOUR_USERNAME

---

⭐ If this project is useful, consider starring the repository.