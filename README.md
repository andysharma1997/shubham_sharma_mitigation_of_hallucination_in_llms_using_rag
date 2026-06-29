# Mitigating Hallucinations in Large Language Models Using RAG

This repository contains the complete codebase and evaluation framework for a systematic empirical evaluation of Retrieval-Augmented Generation (RAG) pipeline configurations. It is designed to identify which combinations of retrieval strategy, chunking approach, retrieval depth, and citation grounding most effectively reduce hallucinations in BFSI (Banking, Financial Services, and Insurance) policy question answering.

The study executes a fully crossed factorial experimental design yielding **96 distinct pipeline configurations**, evaluated across 150 questions from the FinanceBench dataset, producing 14,400 total observations.

---

## ⚙️ Experimental Variable Matrix

The pipeline holds the LLM (LLaMA 3.2-3B) and Embedding model (qwen3-embedding:4b) constant while varying four independent variables:

* **Retrieval Strategy (3):** Dense (TurboQuant), Sparse (BM25), Hybrid (Reciprocal Rank Fusion, k=60).
* **Chunking Strategy (4):** Fixed-256 tokens, Fixed-512 tokens, Semantic Splitter, Sentence-Window (±2).
* **Retrieval Depth (top-k) (4):** k=1, k=3, k=5, k=10.
* **Citation Grounding (2):** Disabled (Standard Prompt), Enabled (Explicit Passage Attribution).

---

## 📁 Repository Structure

```text
shubham_sharma_mitigation_of_hallucination_in_llms_using_rag/
├── .gitignore
├── final_results.csv
├── LICENSE
├── Mitigation_of_Hallucination_using_RAG.ipynb
├── README.md
└── rq1_rq3_summary_chart.png

```

---

## 🛠️ Setup Instructions

This experiment is designed to be executed entirely within a **Google Colab** environment.

### 1. Google Colab Environment Setup

* **Dependencies:** You do not need to manually install dependencies via a `requirements.txt` file. All required packages and dependencies are already available and configured within the notebook.
* **Google Drive Mount:** You will need to mount the notebook to your Google Drive. This ensures that all intermediate and final results are safely saved and accessible after the runtime disconnects.
* **Hugging Face Token:** To access the tokenizer, you will need to add your Hugging Face access token. Save this token under the name `my_hf_token` within your Colab Notebook Secrets.

### 2. Model Serving (Ollama)

The pipeline relies on local inference to ensure reproducibility without API costs. **The complete Ollama server setup and installation are provided within the notebook itself.** When executed, the notebook will automatically configure the server and pull the following necessary models:

* **Generation Backbone:** `llama3.2:3b`
* **Dense Embedding Model:** `qwen-embedding:4b`
* **Judge Model for G-Eval (Hallucination Rate measurement):** `deepseek-r1:8b`


### 3. Data Preparation

Data processing is fully managed automatically within the notebook. You do not need to manually download or upload files:

1. **SEC Filings:** The source SEC filing PDFs are automatically downloaded into the Colab environment via `git clone`.
2. **Evaluation Queries:** The FinanceBench dataset queries (which include the `question` and `answer` ground truths) are fetched programmatically using the Hugging Face `datasets` library (`load_dataset`).

---

## 🚀 Running the Experiment

To execute the 96-condition factorial loop, simply run the cells in the provided notebook (`Mitigation_of_Hallucination_using_RAG.ipynb`) sequentially.

**Compute Requirements & What to Expect:**

* **Hardware:** All experimentation was conducted in Google Colab utilizing an **A100 GPU** machine.
* **Compute Time:** Generating 14,400 LLM responses and evaluating them locally via RAGAS/G-Eval is extremely compute-intensive. The full pipeline requires approximately **90-100 hours of compute**.
* **Ingestion & Caching:** The notebook will first read, normalize, and extract tables from the PDFs. This takes a few minutes but is cached to your Drive for subsequent runs.
* **Indexing:** The script builds the TurboQuant dense index and BM25 sparse index for the current chunking strategy.
* **Output:** Upon completion (or manual interrupt, if saving iteratively), the raw metrics are saved directly to your mounted Drive.

---

## 📊 Statistical Analysis

Once the experiment is complete and the `final_results.csv` is populated, you can perform the inferential statistical analysis to address Research Questions 1 through 4.

The statistical analysis is integrated into the workflow. Executing the relevant notebook sections will perform the following:

* **RQ1 (Retrieval Strategies):** Executes Kruskal-Wallis H-tests and post-hoc Dunn's tests (Bonferroni corrected) to compare Hallucination Rate and Faithfulness across Dense, Sparse, and Hybrid methods.
* **RQ2 (Chunking Strategies & Interactions):** Evaluates Context Precision using Kruskal-Wallis. Crucially, it passes the data to R via `rpy2` to perform an ART ANOVA, testing for statistically significant interaction effects between Chunking Strategy and Retrieval Strategy.
* **RQ3 (Diminishing Returns):** Plots the Accuracy/Cost trade-off curve across k ∈ {1, 3, 5, 10} and calculates the marginal gain to identify the exact point of diminishing returns. (Outputs `rq1_rq3_summary_chart.png`).
* **RQ4 (Citation Grounding):** Conducts a paired Wilcoxon Signed-Rank test to determine if explicit prompt-level citation instructions produce a statistically significant improvement in faithfulness over the standard prompt, reporting Cliff's Delta for effect size.

---

## ✍️ Author

* **Shubham Sharma**
* [LinkedIn Profile](https://www.linkedin.com/in/shubham-sharma-andy/)



```

```