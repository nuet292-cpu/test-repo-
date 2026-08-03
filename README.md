# 📝 Local RAG System with LLMs

> **Build a fully private, offline Retrieval-Augmented Generation (RAG) system** for managing and querying your personal documents — no cloud required.

[![Python](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python)](https://www.python.org/)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.39.0-FF4B4B?logo=streamlit)](https://streamlit.io/)
[![OpenSearch](https://img.shields.io/badge/OpenSearch-2.19.2-005EB8?logo=opensearch)](https://opensearch.org/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLMs-black?logo=ollama)](https://ollama.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

---

## 📸 UI Preview

| Welcome Page | Chatbot in Action |
|:---:|:---:|
| ![Welcome Page](images/ui-1.png) | ![Chatbot Page](images/ui-2.png) |

---

## 🌟 Key Features

| Feature | Description |
|---|---|
| 🔒 **Privacy-First** | All processing is local — no data leaves your machine |
| 🔍 **Hybrid Search** | Combines BM25 keyword search + semantic vector search via OpenSearch |
| 🤖 **Local LLM Chat** | Conversational Q&A powered by Ollama (supports Qwen3, LLaMA, etc.) |
| 📄 **PDF Ingestion** | Upload PDFs, extract text, chunk, embed, and index automatically |
| 👁️ **OCR Fallback** | Uses Tesseract OCR for image-heavy or scanned PDFs |
| ⚙️ **Configurable** | Tune embedding models, chunk size, LLM, and search weights in one file |

---

## 🗂️ Project Structure

```
Basic_RAG/
│
├── Welcome.py                         # 🏠 Streamlit entry point (home page)
│
├── pages/
│   ├── 1_🤖_Chatbot.py               # 💬 Chatbot page with RAG toggle
│   └── 2_📄_Upload_Documents.py      # 📁 PDF upload, indexing & management
│
├── src/                               # 🧠 Core backend logic
│   ├── constants.py                   # ⚙️  All configurable settings
│   ├── embeddings.py                  # 🔢 Sentence-Transformer embedding model
│   ├── chat.py                        # 🗣️  Ollama LLM chat & prompt builder
│   ├── ingestion.py                   # 📥 OpenSearch index creation & bulk indexing
│   ├── opensearch.py                  # 🔍 Hybrid search (BM25 + kNN)
│   ├── ocr.py                         # 👁️  Tesseract OCR for scanned PDFs
│   ├── utils.py                       # 🛠️  Text chunking, cleaning, logging
│   ├── index_config.json              # 📋 OpenSearch index mapping & settings
│   └── __init__.py
│
├── notebooks/
│   ├── 01_Prerequisites_and_Environment_Setup.ipynb
│   ├── 02_OpenSearch_Index_and_Ingestion_standalone.ipynb
│   └── 03_Hybrid_Search_and_Retrieval.ipynb
│
├── images/
│   ├── ui-1.png                       # Welcome page screenshot
│   └── ui-2.png                       # Chatbot page screenshot
│
├── uploaded_files/                    # 📂 Stores uploaded PDFs locally
├── embedding_model/                   # 🗄️  Optional: cached local embedding model
├── logs/                              # 📝 Application log files
│
├── requirements.txt                   # Python dependencies
├── pyproject.toml                     # Project metadata
├── mypy.ini                           # Type-checking config
└── .gitignore
```

---

## ⚡ Quick Start

### Step 1 — Prerequisites

Make sure these are installed on your system:

| Tool | Version | Link |
|---|---|---|
| Python | 3.11+ | https://www.python.org/downloads/ |
| Docker Desktop | Latest | https://www.docker.com/products/docker-desktop/ |
| Ollama | Latest | https://ollama.com/download |
| Tesseract OCR | Latest | See [OCR Setup](#6--ocr-setup-tesseract--poppler) |
| `uv` (fast pip) | Latest | `pip install uv` |

---

### Step 2 — Clone & Set Up Environment

```bash
# Clone the repository
git clone https://github.com/Aman554-EQ/basic-RAG-pipeline.git
cd basic-RAG-pipeline

# Create and activate a virtual environment
python -m venv .venv

# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate

# Install kernel support (for notebooks)
uv pip install ipykernel
python -m ipykernel install --user --name=basic-rag --display-name "Python (Basic RAG)"
```

---

### Step 3 — Install Python Dependencies

```bash
uv pip install -r requirements.txt
```

**Dependencies installed:**

| Package | Version | Purpose |
|---|---|---|
| `streamlit` | 1.39.0 | Web UI framework |
| `sentence-transformers` | 3.4.1 | Embedding model |
| `opensearch-py` | 2.7.1 | OpenSearch Python client |
| `ollama` | 0.3.3 | Local LLM inference |
| `PyPDF2` | 3.0.1 | PDF text extraction |
| `pytesseract` | 0.3.13 | OCR for scanned PDFs |
| `pillow` | 10.4.0 | Image processing |
| `torch` | 2.6.0 | ML tensor operations |
| `numpy` | 2.1.2 | Numerical computing |
| `requests` | 2.32.3 | HTTP client |

---

### Step 4 — Start Ollama & Pull a Model

```bash
# Pull the recommended model (8B parameters, good quality/speed tradeoff)
ollama pull qwen3:8b

# Or use a lighter model
ollama pull llama3.2:1b
```

> **Note:** The default model is `llama3.2:1b` as set in `src/constants.py`. Change `OLLAMA_MODEL_NAME` to match whichever model you pull.

Browse available models: https://ollama.com/library

---

### Step 5 — Start OpenSearch with Docker

**5.1 — Pull and run OpenSearch:**

```bash
docker pull opensearchproject/opensearch:2.19.2
docker pull opensearchproject/opensearch-dashboards:2.19.2

# Start OpenSearch node
docker run -d --name opensearch \
  -p 9200:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "DISABLE_SECURITY_PLUGIN=true" \
  opensearchproject/opensearch:2.19.2

# Start OpenSearch Dashboard (optional but recommended)
docker run -d --name opensearch-dashboards \
  -p 5601:5601 \
  --link opensearch:opensearch \
  -e "OPENSEARCH_HOSTS=http://opensearch:9200" \
  -e "DISABLE_SECURITY_DASHBOARDS_PLUGIN=true" \
  opensearchproject/opensearch-dashboards:2.19.2
```

Visit **http://localhost:5601** to access the OpenSearch Dashboard. ✅

**5.2 — Configure the Hybrid Search Pipeline:**

Open the OpenSearch Dashboard → **Dev Tools** → paste and run:

```json
PUT /_search/pipeline/nlp-search-pipeline
{
  "description": "Post processor for hybrid search",
  "phase_results_processors": [
    {
      "normalization-processor": {
        "normalization": {
          "technique": "min_max"
        },
        "combination": {
          "technique": "arithmetic_mean",
          "parameters": {
            "weights": [0.3, 0.7]
          }
        }
      }
    }
  ]
}
```

> The `0.3 / 0.7` weights favour semantic (vector) search over keyword (BM25) matching. Adjust to your preference.

---

### Step 6 — OCR Setup (Tesseract + Poppler)

Required for processing scanned or image-heavy PDFs.

**macOS:**
```bash
brew install tesseract poppler
```

**Windows:**
1. Install **Tesseract** from [UB Mannheim](https://github.com/UB-Mannheim/tesseract/wiki) or via winget:
   ```powershell
   winget install Tesseract-OCR
   ```
2. Install **Poppler** — download from [oschwartz10612/poppler-windows](https://github.com/oschwartz10612/poppler-windows/releases) and add `C:\poppler\<version>\bin` to your system `PATH`.

**Linux:**
```bash
sudo apt install tesseract-ocr poppler-utils
```

**Install Python OCR libraries:**
```bash
uv pip install pytesseract pdf2image Pillow fpdf2
```

> **Windows tip:** If Tesseract isn't found automatically, set the path in `src/ocr.py`:
> ```python
> pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"
> ```

---

### Step 7 — Configure the App

Edit [`src/constants.py`](src/constants.py) to match your environment:

```python
# ── Embedding model ──────────────────────────────────────────────
EMBEDDING_MODEL_PATH = "sentence-transformers/all-MiniLM-L6-v2"
# Use "embedding_model/" to point to a local cached copy
ASSYMETRIC_EMBEDDING = False   # Set True for asymmetric models (e.g. msmarco)
EMBEDDING_DIMENSION  = 384     # Must match the model's output dimension

# ── Text chunking ─────────────────────────────────────────────────
TEXT_CHUNK_SIZE = 300          # Characters per chunk

# ── Ollama LLM ───────────────────────────────────────────────────
OLLAMA_MODEL_NAME = "llama3.2:1b"   # Any model pulled via `ollama pull`

# ── OpenSearch (do not change unless self-hosting differently) ────
OPENSEARCH_HOST  = "localhost"
OPENSEARCH_PORT  = 9200
OPENSEARCH_INDEX = "documents"
```

---

### Step 8 — Run the App 🚀

```bash
streamlit run Welcome.py
```

Open **http://localhost:8501** in your browser.

---

## 🖥️ App Pages

### 🏠 Welcome (`Welcome.py`)
The landing page. Introduces the app and provides navigation to the sidebar pages.

### 🤖 Chatbot (`pages/1_🤖_Chatbot.py`)
- Type any question into the chat input
- **Enable RAG mode** (sidebar toggle) to ground answers in your uploaded documents via hybrid search
- Adjust **Number of Results in Context Window** and **Response Temperature** from the sidebar
- Chat history is maintained within the session

### 📄 Upload Documents (`pages/2_📄_Upload_Documents.py`)
- Drag and drop one or more **PDF files**
- Text is extracted → chunked → embedded → indexed into OpenSearch automatically
- Manage uploaded documents (view, delete) from the expandable panel
- Supports both native PDF text extraction and **OCR fallback** for scanned pages

---

## 🏗️ Architecture

```
User Query
    │
    ▼
┌─────────────────┐
│  Streamlit UI   │  ← pages/1_🤖_Chatbot.py
└────────┬────────┘
         │ query text
         ▼
┌─────────────────┐        ┌──────────────────────────┐
│  Embedding      │        │  OpenSearch (Docker)      │
│  Model          │───────▶│  Hybrid Search Pipeline   │
│  (MiniLM-L6-v2) │  embed │  ┌──────────┬──────────┐ │
└─────────────────┘  query │  │  BM25    │  kNN     │ │
                           │  │ (0.3 wt) │ (0.7 wt) │ │
                           │  └──────────┴──────────┘ │
                           └────────────┬─────────────┘
                                        │ top-k chunks
                                        ▼
                           ┌─────────────────────────┐
                           │  Prompt Builder          │
                           │  (context + history)     │
                           └────────────┬────────────┘
                                        │ full prompt
                                        ▼
                           ┌─────────────────────────┐
                           │  Ollama  (local LLM)     │
                           │  streaming response      │
                           └────────────┬────────────┘
                                        │
                                        ▼
                              Response in Chat UI
```

**Document Ingestion Flow:**

```
PDF Upload
    │
    ▼
┌───────────────┐    ┌──────────────┐    ┌──────────────────┐
│ Text Extract  │───▶│ Text Chunker │───▶│ Embedding Model  │
│ (PyPDF2 /OCR) │    │ (300 chars)  │    │ (MiniLM-L6-v2)   │
└───────────────┘    └──────────────┘    └────────┬─────────┘
                                                  │ vectors
                                                  ▼
                                    ┌─────────────────────────┐
                                    │  OpenSearch Bulk Index   │
                                    │  { text, embedding,      │
                                    │    document_name }       │
                                    └─────────────────────────┘
```

---

## 📓 Jupyter Notebooks

The `notebooks/` folder contains step-by-step exploration notebooks:

| Notebook | Description |
|---|---|
| `01_Prerequisites_and_Environment_Setup.ipynb` | Install Docker, Ollama, verify all dependencies |
| `02_OpenSearch_Index_and_Ingestion_standalone.ipynb` | Index creation, document ingestion, and embedding walkthrough |
| `03_Hybrid_Search_and_Retrieval.ipynb` | Run hybrid BM25 + kNN searches and inspect results |

> **To use notebooks:** Select the `basic-rag` kernel (installed in Step 2) via **Kernel → Change Kernel**.

---

## 🧩 Source Module Reference

| Module | File | Responsibility |
|---|---|---|
| Constants | [`src/constants.py`](src/constants.py) | All configurable app settings |
| Embeddings | [`src/embeddings.py`](src/embeddings.py) | Load `SentenceTransformer`, generate embeddings |
| Chat | [`src/chat.py`](src/chat.py) | Build prompts, call Ollama, stream responses |
| Ingestion | [`src/ingestion.py`](src/ingestion.py) | Create/delete index, bulk-index documents |
| OpenSearch | [`src/opensearch.py`](src/opensearch.py) | Client init, hybrid search query |
| OCR | [`src/ocr.py`](src/ocr.py) | Extract text from scanned/image PDFs via Tesseract |
| Utils | [`src/utils.py`](src/utils.py) | Text chunking, cleaning, logging setup |
| Index Config | [`src/index_config.json`](src/index_config.json) | OpenSearch mapping (kNN + BM25 settings) |

---

## 🔧 Troubleshooting

| Problem | Solution |
|---|---|
| `docker: command not found` | Reboot after Docker Desktop install; ensure it's on `PATH` |
| `ollama: connection refused` | Run `ollama serve` in a separate terminal |
| `opensearch connection error` | Confirm Docker container is running: `docker ps` |
| `tesseract not found` | Set `pytesseract.pytesseract.tesseract_cmd` path in `src/ocr.py` |
| `embedding model download slow` | Pre-cache with `sentence-transformers` or point to `embedding_model/` dir |
| Streamlit `ModuleNotFoundError` | Ensure `.venv` is activated before running `streamlit run Welcome.py` |

---

## 🛣️ Roadmap / Ideas

- [ ] Support DOCX and TXT file formats
- [ ] Add document preview in the Upload page
- [ ] Multi-user session support
- [ ] Persistent chat history across sessions
- [ ] Re-ranking with cross-encoder models
- [ ] Docker Compose file for one-command startup

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

<div align="center">
  Made with ❤️ for privacy-first AI
</div>
