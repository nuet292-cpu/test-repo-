# RAG Chatbot — Chat with Your Own Documents

This project lets you run a fully local **RAG-based (Retrieval-Augmented Generation)** chatbot using your own PDFs or web content. Ask questions in natural language and get answers grounded in the actual contents of your documents.

![1_tEelGLOyg6n7oUJ0a1fMUA](https://github.com/user-attachments/assets/ec00a9d7-53f7-4c52-b51c-0c565f92521c)

Built with:

- [LangChain](https://www.langchain.com/) — orchestration and prompt management
- [FAISS](https://github.com/facebookresearch/faiss) — fast semantic vector search
- [Ollama](https://ollama.com) — run open-source LLMs locally
- [Streamlit](https://streamlit.io) — interactive chat interface

---

## ✨ Features

- 📄 Upload PDFs or paste website URLs as your knowledge source
- 🧠 Store document chunks as embeddings in a local FAISS vector store
- 🔍 Retrieve relevant context using semantic similarity search
- 💬 Generate context-aware answers via a local LLM
- 💾 Persist conversation history across page refreshes
- 💻 100% local — no API keys, no data sent to the cloud

---

## 🚀 Getting Started

### 1. Install Ollama

Download and install [Ollama](https://ollama.com/download) for your operating system.

Then pull the two required models — the **chat model** and the **embedding model**:

```bash
ollama pull llama3.2:1b
ollama pull nomic-embed-text
```

> **Note:** Both models are required. `llama3.2:1b` handles question-answering and `nomic-embed-text` handles document embedding.

---

### 2. Clone the Repository

```bash
git clone https://github.com/Aman554-EQ/RAG-Ollama-LangChain.git
cd RAG-Ollama-LangChain
```

---

### 3. Create and Activate a Virtual Environment

```bash
# Create virtual environment
python -m venv .venv

# Activate on Windows
.venv\Scripts\activate

# Activate on macOS / Linux
source .venv/bin/activate
```

---

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

---

### 5. Run the App

```bash
streamlit run src/chat_ui.py
```

The app will open automatically at [http://localhost:8501](http://localhost:8501).

---

## 🐳 Docker Setup (Optional)

To run the app inside a Docker container:

```bash
# Build the image
docker build -t rag-chatbot .

# Run the container
docker run -p 8501:8501 rag-chatbot
```

Then open [http://localhost:8501](http://localhost:8501) in your browser.

> **Note:** The container needs access to a running Ollama instance. Make sure Ollama is running on your host machine and configure it to accept connections (e.g., set `OLLAMA_HOST=host.docker.internal` if needed).

---

## 📂 Usage

Once the app is running:

1. **Upload PDFs** via the sidebar — they are chunked, embedded, and stored in the FAISS index.
2. **Add website URLs** in the sidebar text area — web pages are fetched, chunked, and indexed the same way.
3. **Ask questions** in the chat input — the app retrieves the most relevant document chunks and answers using the local LLM.
4. **Reset** the conversation at any time using the 🗑️ button in the sidebar.

---

## 🏛️ Architecture

![1_gXq3HJeXbPO2aGgFDYh0TA](https://github.com/user-attachments/assets/b492d7a7-d280-40ff-b92b-534cd1c415e7)

| Component | Description |
|---|---|
| **`chat_ui.py`** | Streamlit interface — handles file uploads, URL input, chat display, and session state |
| **`llm_rag.py`** | Core RAG handler — retrieves context from the vector store, builds prompts, calls the LLM, and maintains conversation history |
| **`vector_store.py`** | FAISS wrapper — loads, chunks, embeds, and stores documents; persists the index to disk |
| **`conversation.py`** | Conversation persistence — serializes and deserializes chat history to/from a local JSON file |

### Flow

```
User Question
     │
     ▼
Vector Store ──► Retrieve top-k similar chunks
     │
     ▼
Prompt Template (question + context + history)
     │
     ▼
Local LLM (Ollama / llama3.2:1b)
     │
     ▼
Answer displayed in chat
```

---

## 📁 Project Structure

```
RAG-Ollama-LangChain/
├── src/
│   ├── chat_ui.py          # Streamlit app entry point
│   ├── llm_rag.py          # LLM + RAG logic
│   ├── vector_store.py     # FAISS vector store management
│   └── conversation.py     # Conversation history persistence
├── Dockerfile              # Docker configuration
├── requirements.txt        # Python dependencies
└── README.md
```

> Runtime-generated directories (`faiss_index/`, `uploaded_pdfs/`, `conversation.json`) are excluded from version control via `.gitignore`.

---

## ⚠️ Limitations

- Initial PDF parsing and embedding may take several seconds for large files.
- Response latency depends on the chosen LLM and your hardware.
- The chatbot runs only locally; cloud deployment requires additional configuration for Ollama connectivity.

---

## 💡 Ideas for Future Improvements

- History-aware retrieval (query rewriting based on conversation context)
- Tool calling and agentic RAG
- Additional data sources (Google Drive, Notion, local folders)
- Cloud deployment
- Document summarization and answer citation

---

## 📄 License

MIT License. See [LICENSE](LICENSE) for details.
