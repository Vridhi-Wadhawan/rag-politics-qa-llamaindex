# Retrieval-Augmented Question Answering (RAG) on Indian Prime Ministers

This project implements an end-to-end **Retrieval-Augmented Question Answering (RAG)** system
to answer fact-based political questions using Wikipedia data on Indian Prime Ministers.

The focus of this repository is on **RAG system design, retrieval quality, and evaluation** —
not on model fine-tuning or prompt-only experimentation.

---

## System Architecture

![RAG System Architecture](rag_architecture.png)

---

## Problem Statement
Given a set of quiz-style questions related to Indian Prime Ministers, the goal is to
retrieve relevant historical context from Wikipedia and generate **accurate, grounded answers**
using a retrieval-augmented generation pipeline.

---

## Data Ingestion

- Wikipedia pages of all Indian Prime Ministers were collected using `wikipedia-api`
- Each page was stored as a document with metadata:
  - `document_id` = Prime Minister’s name
- This metadata was later used to analyze retrieval accuracy

---

## Indexing Pipeline

### Text Chunking
- Documents were split into manageable chunks using:
  - **TokenTextSplitter**
  - Overlap-based chunking to preserve context continuity

### Embedding & Vector Index
- Dense embeddings generated using **SentenceTransformers**
- Chunks stored in a **LlamaIndex VectorStore**
- The index was persisted and reused for downstream retrieval

---

## Retrieval & Generation

### Retriever
- **Top-K similarity search** implemented using LlamaIndex
- Experiments conducted with:
  - `Top-K = 1`
  - `Top-K = 2`

### Answer Generation
- Retrieved context chunks passed to an LLM for answer generation
- Models used:
  - **FLAN-T5** (primary answer generation)
  - **Llama 3** (indexing and retrieval-side usage)

Generated answers are explicitly **context-grounded** to reduce hallucinations.

---

## RAG Enhancements

To improve retrieval quality and answer stability, the following enhancements were applied:

- **Query Expansion**
  - WH-word augmentation (who, when, where, how)
- **Semantic Reranking**
  - Retrieved chunks reordered using cosine similarity
- **Prompt Ensembling**
  - Multiple prompt styles (direct, concise, WH-aware)
- **Few-shot Prompting**
  - Automatically derived from correctly answered examples
- **Confidence-based Re-querying**
  - Low-confidence answers (< threshold) trigger re-generation

These enhancements significantly reduced dependency on higher Top-K retrieval.

---

## Evaluation Strategy

Evaluation was conducted against a labeled quiz dataset of political questions.

### Metrics Used
- **Jaccard Similarity**
  - Measures lexical overlap between predicted and ground-truth answers
- **Semantic Similarity**
  - Cosine similarity between embedding representations
- **Confidence Calibration**
  - Similarity between generated answers and retrieved context

---

## Results Summary

- Baseline RAG performance:
  - Top-K = 1 → **0.19**
  - Top-K = 2 → **0.32**
- Enhanced RAG performance:
  - Accuracy improved to **0.37**
  - Performance stabilized across Top-K values
- Semantic accuracy (~0.63) and confidence (~0.38) also improved

The enhanced pipeline demonstrated **better grounding, stability, and retrieval focus**.

---

## Scope & Notes

- This repository focuses exclusively on:
  - RAG system design
  - Retrieval quality
  - Evaluation methodology
- Model fine-tuning and Text-to-SQL components are intentionally excluded
- This project emphasizes **applied system thinking over model novelty**

---

## Tech Stack

- Python
- LlamaIndex
- SentenceTransformers
- FLAN-T5
- Llama 3
