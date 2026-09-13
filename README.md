# Drugstore AI Assistant — RAG & Agentic AI

## Project Summary

The **Drugstore AI Assistant** is an AI-powered question-answering system built around a drugstore product database containing 100 products.

The system combines **Retrieval-Augmented Generation (RAG)** with an **agentic multi-tool architecture**. It can answer product, price, category, and symptom-related questions while grounding its answers in the provided database.

### Team Members

- **Amit Shraga**
- **Michal Perry**
- **Yuval Shaanan**

---

## Dataset

The project uses `drugstore_100_items.xlsx`, containing **100 drugstore products**.

Main fields include:

- Item
- Category
- Brand
- Size / Pack
- Primary Use
- Typical Price (ILS)

Prices are converted into a numeric column to support exact calculations and filtering.

---

## Model & Methods

### Language Model

We use **Qwen2.5-7B-Instruct**, loaded with **4-bit NF4 quantization** using BitsAndBytes. This reduces GPU memory usage and allows the model to run efficiently on a Google Colab T4 GPU.

> **Note:** The current implementation does not use LoRA fine-tuning. The model is used as a pretrained model with 4-bit quantization.

### RAG Pipeline

The system uses two retrieval methods:

- **FAISS** — semantic similarity search using `all-MiniLM-L6-v2` embeddings.
- **BM25** — keyword-based retrieval.

The two retrieval methods are combined to improve search quality.

For numerical questions, the system uses **Pandas table queries** instead of semantic retrieval, allowing exact operations such as averages, counts, minimums, maximums, and price filtering.

### Agentic Structure

The multi-tool agent can iteratively select between:

- `search_products`
- `by_category`
- `semantic_search`
- `query_table`
- `calculator`

The agent observes each tool result and can then select another tool before producing the final answer.

Example:

```text
User Question
     ↓
LLM Agent
     ↓
Tool Call
     ↓
Observation
     ↓
Another Tool Call
     ↓
Final Answer
```

---

## Results & Examples

The system successfully answered questions such as:

| Question | Result |
|---|---|
| Total number of products? | **100** |
| Most expensive product? | **Omron Blood Pressure Monitor — ₪220** |
| Price of Nurofen? | **₪22** |
| Average product price? | **₪36.09** |
| Cheapest headache option? | **Aspirin 100 mg — ₪12** |

### Agentic Example

For:

> *"I have a headache. What is the cheapest option, and how many can I buy with 100 ILS?"*

The agent:

1. Searches the **Pain Relief** category.
2. Finds Aspirin 100 mg at **₪12**.
3. Uses the calculator: `100 / 12`.
4. Returns that approximately **8 units** can be purchased.

The project also includes an interactive **ipywidgets GUI** inside Google Colab.

---

## Setup

### Requirements

Install the dependencies with:

```bash
pip install -r requirements.txt
```

`requirements.txt`:

```text
pandas
openpyxl
numpy
sentence-transformers
faiss-cpu
rank-bm25
transformers
accelerate
bitsandbytes
torch
ipywidgets
```

### Run with Google Colab

1. Open `svgdrugstore_ai_assistant_rag_agentic_ai.ipynb`.
2. Select a **T4 GPU** runtime.
3. Place `drugstore_100_items.xlsx` in Google Drive.
4. Run the notebook cells in order:
   - Step 1 — Data & Retrieval
   - Step 2 — Model
   - Step 3 — RAG Pipeline
   - Step 4 — Multi-Tool Agent
   - Step 5 — GUI

---

## Conclusion

This project demonstrates how **RAG, hybrid retrieval, LLM tool calling, and agentic reasoning** can be combined to create a database-grounded drugstore assistant. The system provides both accurate structured answers and natural-language product retrieval while running efficiently on a Colab T4 GPU.