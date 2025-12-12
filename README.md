# 🤖 RAG-based Financial Analysis Chatbot: Project Summary

## 🎯 I. Project Goal

The primary objective is to develop a **Retrieval-Augmented Generation (RAG) based chatbot** for **Quality Assurance (QA)** in the financial sector. It addresses the need for **accurate and timely information** by integrating advanced **Natural Language Processing (NLP)** and robust data sources to support **data-driven decision-making**.

## 🧠 II. Model & Architecture

The system utilizes a fine-tuned **Large Language Model (LLM)** (ChatGPT-4o) combined with RAG techniques for real-time analysis. 

### A. Core Components

* **Retrieval:** **Dense Passage Retrieval (DPR)** encoders convert context and user queries into dense vectors. A **FAISS Index** is used for fast, scalable retrieval of top-$k$ relevant documents.
* **Generation:** A **DPR Reader** processes the retrieved documents and queries to generate precise answers by identifying start/end tokens.
* **Optimization:** **Low-Rank Adaptation (LoRA)** and **model quantization** are employed to reduce memory usage and enhance inference speed.
* **Key Models:** Fine-tuned Chatgpt-4o Model and Facebook/dpr-reader-single-nq-base Model.

### B. Training Pipeline (Orchestrated by Cloud Composer)

The training pipeline is containerized and deployed via **Cloud Run**.

| Task | Description |
| :--- | :--- |
| `load_data_from_gcs` | Retrieves preprocessed JSON data from GCS. |
| `load_model_tokenizer` | Initializes the base model and tokenizer with quantization settings. |
| `load_peft_model` | Implements LoRA using PEFT configurations. |
| `train_model` | Executes model training using SFTTrainer; logs loss metrics. |
| `evaluate_model` | Assesses performance using metrics like BLEU and ROUGE-L score. |
| `containerization/deployment` | Model containerized via Docker and deployed via Cloud Run. |

## 💾 III. Data & Pipeline

### A. Datasets

The foundation relies on two primary datasets:

* **Stock Prices:** Historical and real-time market data (open, close, high, low prices, trading volumes).
    * **Sources:** Yahoo Finance API, Alpha Vantage API, Quandl.
* **Financial News:** Articles, press releases, and reports for qualitative insights into market sentiments.
    * **Sources:** Bloomberg News, Reuters Financial News, NewsAPI.

### B. Data Preprocessing

* **Cleaning:** Removing duplicates, handling missing values (Stock Prices); text normalization, removing special characters and stop words (Financial News).
* **Feature Engineering:** Creating moving averages; extracting sentiment and key entities from news.
* **Orchestration:** **Apache Airflow** is set up to programmatically author, schedule, and monitor workflows (DAGs).

## ☁️ IV. GCP Infrastructure & MLOps

The solution leverages a fully managed GCP environment.

| Tool | Role |
| :--- | :--- |
| **Cloud Run** | Primary deployment platform for the chatbot's **REST API** and MLflow server. |
| **Cloud Functions** | Automates **ETL data ingestion** and preprocessing pipelines (event-driven). |
| **GCS** | Centralized storage for raw/processed data, MLflow artifacts, and trained model files. |
| **Cloud Build** | Automates **CI/CD pipeline** for building and pushing Docker images to Artifact Registry. |
| **MLflow** | Tracks machine learning experiments, logging models and metrics (stored in GCS). |
| **Secret Manager** | Securely handles sensitive information (API keys, credentials). |
| **Artifact Registry**| Stores and manages Docker images used by Cloud Run and Cloud Functions. |

## 🚀 V. CI/CD and Monitoring

* **CI/CD:** Automated via **GitHub Actions** and **Cloud Build**. A new Docker image is created for training and deployment upon every push to the `main` branch.
* **Drift Detection:** Multiple feedback loops are used, including click rate on the UI feedback button, changes in user prompt length, and detection of a large number of new words in recent news data.
* **Cost:** Estimated at approximately **$25/month** for the deployment server, plus **$5 per million requests** for the fine-tuned custom ChatGPT-4o model.
