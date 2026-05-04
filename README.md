# RAG System — Q&A

## What This Is

A minimal Retrieval-Augmented Generation (RAG) system that answers user questions
grounded in a PDF knowledge base. Built entirely in a single Jupyter Notebook with
a Gradio UI for live interaction.


## Project Structure

```
rag-project/
├── notebooks/
│   └── RAG.ipynb           ← entire RAG application (main deliverable)
├── chroma_db/              ← auto-created by ChromaDB on first run
├── KB_PCAI.pdf             ← knowledge base document
├── requirements.txt        ← all dependencies
└── README.md
```


## Tech Stack

| Component       | Tool                                        |
|-----------------|---------------------------------------------|
| Knowledge Base  | KB_PCAI.pdf (3-page PDF)                    |
| Document Loader | PyPDFLoader (LangChain)                     |
| Chunking        | RecursiveCharacterTextSplitter (300 / 20)   |
| Embeddings      | OllamaEmbeddings — nomic-embed-text (768d)  |
| Vector Store    | ChromaDB — persisted locally                |
| Retrieval       | Similarity search, TOP_K = 3                |
| LLM             | llama3 via Ollama (runs locally on CPU)     |
| Orchestration   | LangChain LCEL pipe syntax                  |
| UI              | Gradio — runs inside Jupyter Notebook       |


## Setup Steps

### 1. Install Python dependencies
```bash
pip install -r requirements.txt
```

### 2. Pull models via Ollama (one time)
```bash
ollama pull llama3
ollama pull nomic-embed-text
```

### 3. Start Ollama server
```bash
ollama serve
```
Keep this running in a terminal. Verify at: `http://localhost:11434`

### 4. Open and run the notebook
```
Open notebooks/RAG.ipynb in VS Code or Jupyter
Run cells 1 → 8 top to bottom
```

### 5. Use the Gradio UI
Cell 8 will print a local URL like:
```
http://127.0.0.1:7860
```
Open in browser → type your question → click Submit.


## Notebook Cell Guide

| Cell   | What it does                                      |
|--------|---------------------------------------------------|
| Cell 1 | All imports and configuration                     |
| Cell 2 | Check Ollama is running on port 11434             |
| Cell 3 | Load KB_PCAI.pdf and split into chunks            |
| Cell 4 | Load OllamaEmbeddings (nomic-embed-text)          |
| Cell 5 | Build ChromaDB vector store (first time only)     |
| Cell 5b| Load existing ChromaDB (use on notebook restarts) |
| Cell 6 | Set up retriever (TOP_K = 3)                      |
| Cell 7 | Build RAG chain (LLM + prompt + retriever)        |
| Cell 8 | Launch Gradio UI                                  |

> **On restart:** Skip Cell 5, run Cell 5b instead — avoids re-indexing.


## requirements.txt

```
chromadb
langchain
langchain-community
langchain-chroma
langchain-ollama
langchain-text-splitters
gradio
pypdf
ollama
requests
```


## How RAG Works (Simple Explanation)

```
User Question
     │
     ├──► OllamaEmbeddings converts question to vector
     │
     ├──► ChromaDB finds TOP-3 most similar chunks
     │
     ├──► Prompt fills {context} + {question}
     │
     ├──► llama3 reads grounded prompt → generates answer
     │
     └──► Gradio displays answer to user
```

The LLM is instructed to answer ONLY from retrieved context.
If the answer is not found it responds: "I don't know."
This prevents hallucination — called **Grounding**.


## Repository Access

GitHub: [https://github.com/amarjeet-26/rag-project]

Reviewers:
- jose-claudio-calderon
- nishant-chanduka
