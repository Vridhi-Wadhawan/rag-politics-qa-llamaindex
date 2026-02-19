# Retrieval-Augmented Question Answering (RAG) on Indian Prime Ministers

This repository implements an end-to-end **Retrieval-Augmented Question Answering (RAG)** system
for answering fact-based political questions using Wikipedia data on Indian Prime Ministers.

The project emphasizes **RAG system design, retrieval quality, and evaluation methodology**
rather than model fine-tuning or prompt-only experimentation.

---

## System Architecture

![RAG System Architecture](rag_architecture.png)

---

## Problem Statement

Given a set of quiz-style questions about Indian Prime Ministers, the objective is to:
1. Retrieve historically relevant context from Wikipedia, and  
2. Generate **accurate, grounded answers** using a retrieval-augmented generation pipeline.

---

## Data Ingestion

- Wikipedia pages of all Indian Prime Ministers are collected using `wikipedia-api`
- Each Wikipedia page is treated as a **single document**
- Documents are tagged with metadata:
  - `document_id` = Prime Minister’s name
- This metadata is later used to analyze retrieval accuracy and grounding

---

## Indexing Pipeline

### Text Chunking
- Documents are split into overlapping chunks using **TokenTextSplitter**
- Overlap-based chunking preserves contextual continuity across facts

### Embedding & Vector Index
- Dense embeddings generated using **SentenceTransformers**
- Chunks are stored in a **LlamaIndex VectorStore**
- The vector index is persisted locally to ensure reproducibility and avoid recomputation

---

## Retrieval & Generation

### Retriever
- **Top-K similarity search** implemented using LlamaIndex
- Experiments conducted with:
  - `Top-K = 1`
  - `Top-K = 2`

### Answer Generation
- Retrieved context chunks are passed to a language model for answer generation
- Models used:
  - **FLAN-T5** for primary answer generation
  - **Llama 3.1 (via Ollama)** for indexing-time language understanding

Generated answers are explicitly **context-grounded** to minimize hallucinations.

---

## RAG Enhancements

To improve retrieval quality, answer stability, and grounding, the following enhancements were applied:

- **Query Expansion**
  - WH-word augmentation (who, when, where, how)
- **Semantic Reranking**
  - Retrieved chunks reordered using cosine similarity
- **Prompt Ensembling**
  - Multiple prompt styles (direct, concise, WH-aware)
- **Few-shot Prompting**
  - Automatically derived from correctly answered examples
- **Confidence-based Re-querying**
  - Low-confidence answers trigger re-generation

These enhancements significantly reduced reliance on higher Top-K retrieval while improving answer quality.

---

## Evaluation Strategy

Evaluation was conducted using a labeled quiz dataset of political questions.

### Metrics Used
- **Jaccard Similarity**
  - Measures lexical overlap between predicted and ground-truth answers
- **Semantic Similarity**
  - Cosine similarity between embedding representations
- **Confidence Calibration**
  - Similarity between generated answers and retrieved context

---

## Results Summary

- **Baseline RAG performance**
  - Top-K = 1 → **0.19**
  - Top-K = 2 → **0.32**
- **Enhanced RAG performance**
  - Accuracy improved to **0.37**
  - Performance stabilized across Top-K values
- Semantic accuracy (~0.63) and confidence (~0.38) also improved

Overall, the enhanced pipeline demonstrated **better grounding, stability, and retrieval focus**.

---

## Setup & Reproducibility

### Install Dependencies

```bash
pip install -r requirements.txt
```
### Local LLM Requirement

This project uses Llama 3.1 via Ollama.

Ensure Ollama is installed and the model is available locally:

```bash
ollama pull llama3.1:8b-instant
```
### Run the Pipeline

Open and execute:

```bash
rag_pipeline.ipynb
```
The vector index is persisted locally and reused across runs.

---

## Scope & Notes

- This repository focuses on:
  - RAG system design
  - Retrieval quality
  - Evaluation methodology
- Model fine-tuning and Text-to-SQL components are intentionally excluded
- The emphasis is on applied system thinking over model novelty

---

## Tech Stack
- Python
- LlamaIndex
- SentenceTransformers
- FLAN-T5
- Llama 3 (via Ollama)

---
