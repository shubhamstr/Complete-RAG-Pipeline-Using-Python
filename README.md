
# Complete RAG Pipeline Using Python

A complete, local Retrieval-Augmented Generation (RAG) pipeline built from scratch in Python without any frameworks or tools. It utilizes [Ollama](https://ollama.com/) for local model inference, [NumPy](https://numpy.org/) for vector similarity math, and [Requests](https://requests.readthedocs.io/) for API communication, managed with the ultra-fast Python package manager [uv](https://docs.astral.sh/uv/).

---

## 📌 Project Overview

This project implements an end-to-end local RAG pipeline:

1. **Document Ingestion & Chunking**: Reads markdown documents from the `docs/` folder and splits them into logical text paragraphs.
2. **Dense Vector Embeddings**: Embeds text chunks locally using Ollama's embeddings endpoint.
3. **Semantic Search**: Computes cosine similarity scores using NumPy vector operations to retrieve the top-$k$ most relevant chunks for a user's question.
4. **Context-Grounded Generation**: Passes the retrieved context and question to a local LLM with strict instructions to answer only using the provided facts.

---

## 🛠️ Tech Stack & Models

| Component            | Choice / Specification           | Description                                           |
| :------------------- | :------------------------------- | :---------------------------------------------------- |
| **Package Manager**  | [uv](https://docs.astral.sh/uv/) | Fast Python package and environment manager           |
| **Local LLM Engine** | [Ollama](https://ollama.com/)    | Local inference runtime serving REST APIs             |
| **Embedding Model**  | `nomic-embed-text`               | High-performing open embedding model via Ollama       |
| **Chat / LLM Model** | `qwen3:4b-instruct`              | Efficient instruction-tuned language model via Ollama |
| **Core Libraries**   | `numpy` & `requests`             | Vector math (cosine similarity) & HTTP client         |

---

## 🔗 Useful Third-Party Links

- **Ollama**: [https://ollama.com](https://ollama.com)
  - [Ollama Model Library](https://ollama.com/library)
  - [nomic-embed-text Model](https://ollama.com/library/nomic-embed-text)
  - [qwen Model Family](https://ollama.com/library/qwen)
- **uv (Astral)**: [https://docs.astral.sh/uv/](https://docs.astral.sh/uv/)
- **NumPy Documentation**: [https://numpy.org/doc/](https://numpy.org/doc/)
- **Requests Documentation**: [https://requests.readthedocs.io/](https://requests.readthedocs.io/)

---

## 📋 Prerequisites

1. **Python 3.10+** (or manage it directly via `uv`).
2. **Ollama installed and running**:
   - Download and install Ollama from [ollama.com/download](https://ollama.com/download).
   - Verify Ollama is running at `http://localhost:11434`.
3. **uv installed**:
   - **Windows (PowerShell)**:
     ```powershell
     powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
     ```
   - **macOS / Linux**:
     ```bash
     curl -LsSf https://astral.sh/uv/install.sh | sh
     ```

---

## 🚀 Step-by-Step Setup Guide

### 1. Pull Required Models in Ollama

Open your terminal and pull both the embedding and chat models:

```bash
# Pull the embedding model
ollama pull nomic-embed-text

# Pull the chat model
ollama pull qwen3:4b-instruct
```

Verify the models are downloaded:

```bash
ollama list
```

---

### 2. Clone and Open the Repository

```bash
git clone <repository-url>
cd Complete-RAG-Pipeline-Using-Python
```

---

### 3. Install Dependencies Using `uv`

If you are setting up the project from scratch or syncing dependencies from `pyproject.toml` / `uv.lock`:

```bash
# Sync dependencies and automatically create virtual environment (.venv)
uv sync
```

_(Optional)_ If initializing a project from scratch:

```bash
# Initialize a new uv project
uv init --name complete-rag-pipeline-using-python

# Add required dependencies
uv add requests numpy
```

---

### 4. Prepare Your Knowledge Base

Place your source markdown files inside the `docs/` directory:

```text
Complete-RAG-Pipeline-Using-Python/
├── docs/
│   └── handbook.md      # Add your markdown files here
├── .venv/
├── pyproject.toml
├── uv.lock
├── rag.py
└── README.md
```

The script automatically indexes all `.md` files located in the `docs/` folder.

---

### 5. Run the Application

Execute the RAG script using `uv`:

```bash
uv run python rag.py
```

Or activate the virtual environment and run with Python directly:

- **Windows (PowerShell)**:
  ```powershell
  .venv\Scripts\Activate.ps1
  python rag.py
  ```
- **macOS / Linux**:
  ```bash
  source .venv/bin/activate
  python rag.py
  ```

---

## 💬 Usage Example

Once started, the script indexes your documents and prompts you for questions:

```text
Indexed 24 chunks

Ask: What is the remote work policy?

---Content Start---
Employees are eligible to work remotely up to two days a week with manager approval...
---Content End---

According to the policy, employees are eligible to work remotely up to two days per week with manager approval.
```

If the answer is not present in the indexed documents, the model will reply:

```text
I don't know.
```

### 🛑 Graceful Exit

You can safely terminate the application loop at any time:
- Type `exit`, `quit`, or `q` and press Enter.
- Or press `Ctrl + C` (or `Ctrl + D` on macOS/Linux) to exit immediately without an unhandled traceback.

---

## ⚙️ How It Works Under the Hood

```
[ docs/*.md ] ──> Chunking (\n\n) ──> Ollama /api/embed (nomic-embed-text) ──> Vectors in NumPy
                                                                                     │
[ User Query ] ──> Ollama /api/embed ──> Cosine Similarity Matrix Dot Product ───────┘
                                                       │
                                            Top-k Chunks Retrieved
                                                       │
[ Context + Prompt ] ──> Ollama /api/chat (qwen3:4b-instruct) ──> Final Grounded Answer
```

- **Cosine Similarity Formula**:
  $$\text{Cosine Similarity} = \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\|_2 \, \|\mathbf{v}\|_2}$$
  Computed using `chunk_vectors @ q / (np.linalg.norm(chunk_vectors, axis=1) * np.linalg.norm(q))`.

---

## 🧰 Useful `uv` Commands

| Command                  | Purpose                                                                     |
| :----------------------- | :-------------------------------------------------------------------------- |
| `uv sync`                | Synchronize dependencies and recreate `.venv` matching `uv.lock`            |
| `uv run python rag.py`   | Run script within the managed virtual environment without manual activation |
| `uv add <package>`       | Add a dependency to `pyproject.toml` and update the lockfile                |
| `uv remove <package>`    | Remove a package from dependencies                                          |
| `uv python install 3.14` | Download and install a specific Python version                              |
| `uv lock --upgrade`      | Update all locked package dependencies to their latest compatible versions  |

---

## 📄 License

This project is licensed under the MIT License. Feel free to use and modify it for your learning and projects!
