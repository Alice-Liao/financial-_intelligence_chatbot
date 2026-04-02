# 📈 FinSight — Financial Intelligence Chatbot (RAG System)

> A RAG-powered financial Q&A assistant that grounds LLM responses in **11,862 real financial documents**, delivering source-cited answers with **69% higher accuracy** than a standalone LLM baseline.

**Stack:** Streamlit · FastAPI · ChromaDB · Llama 3.2 (3B) · Sentence Transformers · Ollama · Docker

---

## 🏆 Key Results

| Metric | RAG System (Ours) | Baseline Llama 3 | Improvement |
|--------|:-----------------:|:----------------:|:-----------:|
| Overall Score (23 questions) | **7.6 / 10** | 4.5 / 10 | +69% |
| Time-sensitive queries (Q1) | **8.5 / 10** | 2.25 / 10 | +278% |

> On earnings-specific queries, the baseline LLM completely failed (score: 2.25) by outputting knowledge-cutoff refusals. Our RAG system retrieved live data and answered accurately (score: 8.5).

---

## 🧠 Architecture

```
User Query
    │
    ▼
[Sentence Transformers]        ← all-MiniLM-L6-v2 (384-dim embeddings)
    │  encode query
    ▼
[ChromaDB Vector Store]        ← HNSW index, cosine similarity ANN search
    │  top-4 docs retrieved
    ▼
[Context Injection]            ← COSTR prompt engineering framework
    │  grounding prompt
    ▼
[Llama 3.2 3B via Ollama]      ← local inference, no fine-tuning
    │
    ▼
Answer + Source Citations
```

---

## 📊 Dataset

| Source | Entries | Collection Method |
|--------|--------:|-------------------|
| Reddit (r/stocks, r/investing, r/CryptoCurrency) | 7,479 | Public JSON API |
| Yahoo Finance RSS (300+ tickers) | 4,383 | feedparser |
| **Total** | **11,862** | |

**Quality filters applied:** min 100 chars · min 5 upvotes · max 2,000 chars  
**Coverage:** Stocks (AAPL, MSFT, NVDA, TSLA) · Crypto (BTC, ETH) · ETFs (SPY, QQQ, VOO)

---

## 🚀 Features

- **RAG Pipeline** — decouples knowledge base from LLM weights; real-time updates without retraining
- **Semantic Search** — HNSW-indexed vector store with sub-second cosine similarity retrieval
- **Hallucination Reduction** — strict grounding prompt ensures answers are sourced from retrieved docs only
- **Source Citations** — every answer includes document references with relevance scores
- **Edge AI** — runs fully offline on consumer hardware; no API costs, full data privacy
- **FastAPI Backend** — REST API with `/query` endpoint; testable via Swagger at `/docs`

---

## 🛠️ Quick Start

### Prerequisites
- Python 3.8+
- [Ollama](https://ollama.com) installed

```bash
ollama pull llama3.2:3b
```

### Installation

```bash
git clone https://github.com/Alice-Liao/financial-intelligence-chatbot
cd financial-intelligence-chatbot
pip install fastapi uvicorn chromadb sentence-transformers ollama pydantic streamlit requests
```

### Run (3 terminals)

```bash
# Terminal 1 — LLM engine
ollama serve

# Terminal 2 — Backend API (wait for "Uvicorn running on http://0.0.0.0:8000")
python main.py

# Terminal 3 — Frontend UI (opens at http://localhost:8501)
streamlit run app.py
```

### API Usage

```bash
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{"question": "What are the latest earnings surprises?", "n_results": 4, "model": "llama3.2:3b"}'
```

Or use Swagger UI: `http://localhost:8000/docs`

---

## 📁 Project Structure

```
financial-intelligence-chatbot/
├── chroma_db/           # Persisted vector store (.sqlite3 + .bin)
├── data_collector/      # Reddit + Yahoo Finance scrapers
├── main.py              # FastAPI backend
├── app.py               # Streamlit frontend
├── Dockerfile
├── entrypoint.sh
└── requirements.txt
```

---

## 👥 Team

Built by a team of 4 for Northeastern University CS 6120 (NLP), Dec 2025.  
Contributors: Xinmeng Wu · Yuxin Hu · Muqing Cao · **Danyan (Alice) Liao**

---

## 📄 License
MIT
