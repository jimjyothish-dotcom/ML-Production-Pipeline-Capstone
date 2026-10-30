🚀 ML Production Pipeline Capstone

An end-to-end MLOps / Machine Learning production pipeline built to
demonstrate how a machine learning project can move from data ingestion
and preprocessing to model training, evaluation, experiment tracking,
versioning, API serving, containerization, CI/CD, cloud deployment, and
monitoring.

The project is structured as a production-oriented ML system rather than
a notebook-only workflow.

🎯 Project Overview

This project implements a complete ML lifecycle with:

Data ingestion and preprocessing

Feature engineering

Model training and evaluation

MLflow experiment tracking

DVC data and pipeline versioning

Model registration/promotion

FastAPI inference service

Docker containerization

GitHub Actions CI/CD

AWS ECR image management

Amazon EKS deployment

Kubernetes LoadBalancer service

Prometheus monitoring

Grafana dashboards

Automated testing

The repository follows a modular structure under src/ and uses DVC
stages to make the ML workflow reproducible.

🏗️ End-to-End Architecture

                 ┌─────────────────────┐
                 │     Raw Dataset     │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │   Data Ingestion    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Data Preprocessing  │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Feature Engineering │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │   Model Building    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Model Evaluation    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Model Registration  │
                 └──────────┬──────────┘
                            │
                 ┌──────────┴──────────┐
                 │                     │
                 ▼                     ▼
          ┌──────────────┐      ┌──────────────┐
          │ FastAPI API  │      │ Model/MLflow │
          └──────┬───────┘      └──────────────┘
                 │
                 ▼
          ┌──────────────┐
          │ Docker Image │
          └──────┬───────┘
                 │
                 ▼
          ┌──────────────┐
          │   AWS ECR    │
          └──────┬───────┘
                 │
                 ▼
          ┌──────────────┐
          │  Amazon EKS  │
          └──────┬───────┘
                 │
                 ▼
          ┌──────────────┐
          │ LoadBalancer │
          └──────┬───────┘
                 │
        ┌────────┴────────┐
        ▼                 ▼
   Prometheus          Grafana
    Monitoring        Dashboard

🔄 ML Pipeline

The DVC pipeline defines the following stages:

Data Ingestion

Data Preprocessing

Feature Engineering

Model Building

Model Evaluation

Model Registration

DVC tracks generated outputs such as raw/intermediate/processed data,
the vectorizer, trained model, metrics, and experiment information.

The configured parameters include:

data_ingestion:
  test_size: 0.25

feature_engineering:
  max_features: 50

📦 DVC Pipeline

The project uses dvc.yaml to define reproducible pipeline stages.

Reproduce the pipeline

dvc repro

Check pipeline status

dvc status

Push tracked data/artifacts to the configured remote

dvc push

The repository also contains:

.dvc/
dvc.yaml
dvc.lock
params.yaml

📈 Experiment Tracking

The project integrates MLflow and DagsHub for experiment
tracking and artifact management.

The workflow includes:

Experiment tracking

Run metadata

Model/artifact logging

Experiment information

Model registration/promotion

The project flow also documents configuring MLflow with DagsHub and
using experiment tracking during model development.

Configure your own DagsHub/MLflow credentials through environment
variables or your CI/CD secret store. Never commit access tokens or
credentials to the repository.

🧠 Model Lifecycle

The project separates model development into dedicated modules:

src/models/
├── model_building.py
├── model_evaluation.py
└── register_model.py

The DVC pipeline produces:

models/model.pkl
reports/metrics.json
reports/experiment_info.json

This creates a clear separation between model training, evaluation, and
registration.

🌐 FastAPI Inference Service

The trained model is exposed through a FastAPI application.

The Docker configuration runs the API on:

0.0.0.0:5000

Start the application locally according to the FastAPI application
entrypoint in fastapi_app/.

Example:

uvicorn app:app --host 0.0.0.0 --port 5000

The API can then be accessed at:

http://localhost:5000

🐳 Docker

The project includes a production-oriented Dockerfile.

The container:

Uses Python 3.10 slim

Installs dependencies from requirements.txt

Downloads required NLTK resources

Copies the FastAPI application

Copies model artifacts

Exposes port 5000

Starts the FastAPI application with Uvicorn

Build image

docker build -t capstone-app:latest .

Run container

docker run -p 8888:5000 capstone-app:latest

If the application requires the configured secret environment variable:

docker run -p 8888:5000 -e CAPSTONE_TEST=<YOUR_SECRET> capstone-app:latest

Never place real credentials directly in commands that may be stored in
shell history or documentation.

☁️ AWS Deployment

The project documents a cloud deployment workflow using AWS services.

AWS services involved

Amazon S3

Amazon ECR

Amazon EKS

Amazon EC2

AWS IAM

AWS CloudFormation

The CI/CD workflow builds and pushes the Docker image to Amazon ECR
before deployment.

📦 Amazon ECR

The container image is designed to be pushed to an Amazon ECR
repository.

Typical workflow:

Code
  ↓
Docker Build
  ↓
Docker Image
  ↓
Amazon ECR
  ↓
Amazon EKS

Required AWS configuration includes credentials, region, account ID, and
ECR repository settings.

Use GitHub Actions Secrets/Variables for these values instead of
committing credentials.

☸️ Kubernetes / Amazon EKS

The repository contains deployment.yaml for Kubernetes deployment.

The configured deployment:

Runs 2 FastAPI replicas

Uses a Kubernetes Deployment

Exposes container port 5000

Uses CPU and memory requests/limits

Loads a secret through Kubernetes SecretKeyRef

Creates a LoadBalancer service

Kubernetes architecture

             Internet
                 │
                 ▼
        ┌─────────────────┐
        │ LoadBalancer    │
        └────────┬────────┘
                 │
          ┌──────┴──────┐
          ▼             ▼
     ┌─────────┐   ┌─────────┐
     │ FastAPI │   │ FastAPI │
     │ Pod #1  │   │ Pod #2  │
     └─────────┘   └─────────┘

Useful commands

kubectl get nodes
kubectl get pods
kubectl get svc

Apply the deployment:

kubectl apply -f deployment.yaml

🔁 CI/CD

The project contains GitHub Actions workflows under:

.github/workflows/

The documented CI/CD flow covers:

Git Push
   ↓
Tests / Validation
   ↓
Docker Build
   ↓
Push Image to ECR
   ↓
Deploy to EKS

This makes the project suitable for demonstrating automated ML
application delivery.

📊 Monitoring

The project includes a monitoring setup using:

Prometheus

Prometheus is configured to scrape the deployed application.

A typical configuration uses:

scrape_interval: 15s

Grafana

Grafana is used to visualize Prometheus metrics through dashboards.

The documented setup uses:

Prometheus → Grafana

This provides an observability layer around the deployed ML API.

🧪 Testing

The repository includes:

tests/
test.py
test_environment.py
tox.ini

Pytest is included in the project's dependencies.

Run tests with:

pytest

📁 Project Structure

ML_Production_pipeline_capston_pro/
│
├── .dvc/
├── .github/
│   └── workflows/
│
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
│
├── docs/
│
├── fastapi_app/
│
├── mlartifacts/
│
├── models/
│
├── notebooks/
│
├── references/
│
├── reports/
│
├── script/
│   └── promote_model.py
│
├── src/
│   ├── connections/
│   ├── data/
│   ├── features/
│   ├── logger/
│   ├── models/
│   └── visualization/
│
├── tests/
│
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

🛠️ Tech Stack

Machine Learning

Python

NumPy

Pandas

Scikit-learn

NLTK

Joblib

MLOps

MLflow

DVC

DagsHub

Model Registry

Experiment Tracking

API

FastAPI

Uvicorn

Pydantic

DevOps

Docker

GitHub Actions

CI/CD

Pytest

Tox

Cloud & Infrastructure

AWS S3

AWS ECR

Amazon EKS

Amazon EC2

AWS IAM

AWS CloudFormation

Kubernetes

kubectl

eksctl

Monitoring

Prometheus

Grafana

⚙️ Local Setup

1. Clone the repository

git clone https://github.com/YOUR_USERNAME/ML_Production_pipeline_capston_pro.git
cd ML_Production_pipeline_capston_pro

2. Create a virtual environment

The project flow documents Python 3.10:

conda create -n atlas python=3.10
conda activate atlas

Or use a standard Python virtual environment:

python -m venv venv

3. Install dependencies

pip install -r requirements.txt

4. Install the project

pip install -e .

5. Run the DVC pipeline

dvc repro

6. Check generated artifacts

data/
models/
reports/

🔐 Environment Variables & Secrets

The deployment workflow uses secrets for application and cloud
configuration.

Examples include:

CAPSTONE_TEST
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
AWS_ACCOUNT_ID
ECR_REPOSITORY

For production:

Store secrets in GitHub Actions Secrets

Use Kubernetes Secrets for cluster workloads

Use AWS IAM roles where appropriate

Do not commit credentials, API tokens, or private keys

📋 Reproducibility

One of the major goals of this project is reproducibility.

The combination of:

DVC
+ params.yaml
+ dvc.lock
+ MLflow
+ version-controlled source code

allows data, pipeline stages, parameters, and experiments to be tracked
systematically.

🎯 What This Project Demonstrates

This capstone demonstrates practical knowledge of:

End-to-end ML lifecycle management

Production project structure

Data versioning with DVC

Reproducible ML pipelines

Feature engineering

Model training and evaluation

Experiment tracking with MLflow

Model registration

API-based inference

Docker containerization

CI/CD automation

AWS cloud deployment

Kubernetes orchestration

Amazon EKS

Prometheus monitoring

Grafana visualization

Automated testing

🔮 Future Improvements

Potential improvements include:

Add automated data validation

Add model drift detection

Add automated model retraining

Add model performance monitoring

Add a formal model registry workflow

Add security scanning to CI/CD

Add Kubernetes Horizontal Pod Autoscaling

Add centralized logging

Add automated integration tests

Add infrastructure-as-code with Terraform

Add canary or blue-green deployments

⚠️ Security Note

The repository's project-flow documentation contains historical setup
notes involving credentials/secrets. Do not reuse or publish any
credential shown in historical documentation. Rotate any credential
that may have been exposed and use environment variables, GitHub
Secrets, Kubernetes Secrets, or AWS IAM instead.

📄 License

This project is licensed under the MIT License.

👨‍💻 Authors

This project was originally developed as an ML Production Pipeline capstone.

GitHub: https://github.com/YOUR_USERNAME

⭐ If this project is useful, consider starring the repository.
