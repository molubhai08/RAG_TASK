# Policy Document RAG System 📄🤖

This project implements a **Retrieval-Augmented Generation (RAG)** pipeline for answering questions strictly based on **company policy PDFs**. It is designed to minimize hallucinations and provide **fact-based, document-grounded answers** using modern NLP tooling.

---

## ✨ Features

* 📂 Loads and processes multi-page PDF policy documents
* 🧩 Automatically detects and chunks **policy sections**
* 📏 Dynamically selects optimal chunk size using token statistics
* 🔍 Semantic search with **FAISS + HuggingFace embeddings**
* 🧠 Uses **Groq LLaMA 3.3 (70B)** for accurate, low-temperature responses
* 🚫 Built-in refusal mechanism for unanswerable questions
* 🎯 Strict prompt rules to prevent hallucinations

---

## 🏗️ Architecture Overview

```
PDF → Section Split → Token Analysis → Smart Chunking
    → Embeddings → FAISS Vector Store
    → Gated Retrieval → LLM (Groq) → Answer
```

---

## 🛠️ Tech Stack

* **Python**
* **LangChain**
* **FAISS** (Vector Database)
* **HuggingFace Sentence Transformers**
* **Groq API (LLaMA‑3.3‑70B)**
* **tiktoken** (Token analysis)
* **dotenv** (Environment variables)

---

## 📁 Project Structure

```
app.py                # Main RAG pipeline
Company Policies.pdf  # Input policy document
.env                  # Environment variables (GROQ_API_KEY)
README.md             # Project documentation
```

---

## ⚙️ Setup Instructions

### 1️⃣ Install Dependencies

```bash
pip install langchain langchain-community langchain-groq \
            sentence-transformers faiss-cpu tiktoken python-dotenv
```

---

### 2️⃣ Add Environment Variables

Create a `.env` file:

```env
GROQ_API_KEY=your_groq_api_key_here
```

---

### 3️⃣ Add Policy Document

Place your PDF file in the root directory and update the filename if needed:

```python
pdf_path = "Company Policies.pdf"
```

---

### 4️⃣ Run the Application

```bash
python app.py
```

---

## 🔍 How It Works (Step‑by‑Step)

### 📌 1. PDF Loading

* Uses `PyPDFLoader` to extract text from each page

---

### 📌 2. Section‑Based Chunking

* Splits content using regex matching:

  ```
  1. XYZ Policy
  ```
* Filters out very small or noisy sections

---

### 📌 3. Token Statistics & Dynamic Chunking

* Calculates:

  * Min / Avg / Median / Max tokens
  * 75th percentile
* Automatically sets:

  * `CHUNK_SIZE ≈ min(75th percentile, 600)`
  * `CHUNK_OVERLAP = 20%`

---

### 📌 4. Embedding & Vector Storage

* Model: `all-MiniLM-L6-v2`
* Normalized embeddings for cosine similarity
* Stored using FAISS

---

### 📌 5. Gated Retrieval Logic

Only returns chunks when:

* Best similarity score ≥ `0.2`
* Other chunks ≥ `60%` of best score

This avoids irrelevant context injection.

---

### 📌 6. Strict RAG Prompting

The LLM **must**:

* Answer only from retrieved context
* Clearly state missing information
* Refuse when context is insufficient

Refusal message:

```
I cannot answer this question based on the provided documents.
```

---

## 🧪 Example Queries

✔️ Answerable:

* "What are the delivery timelines for express shipping?"

⚠️ Partially Answerable:

* "What factors can delay international shipping?"

❌ Unanswerable:

* "Which cities are part of the same‑day delivery pilot?"

---

## 🔐 Hallucination Control Measures

* Temperature set to `0.0`
* Retrieval score gating
* Context‑only prompt rules
* Explicit refusal handling

---

## 🚀 Use Cases

* Internal policy assistants
* Compliance Q&A bots
* Employee self‑service tools
* Enterprise RAG prototypes

---

## 📌 Notes

* Designed for **accuracy > creativity**
* Works best with structured policy documents
* Easily extensible to other PDFs or domains

---

