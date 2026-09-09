# Interview Trainer — IBM Granite

A fully stateful, RAG-grounded interview coaching web application powered by **IBM Granite on watsonx**.

---

## Architecture

```
interview_trainer/
├── app.py          ← Gradio web UI (all tabs)
├── session.py      ← Session state machine (question gen, eval, adaptive logic)
├── rag.py          ← ChromaDB ingestion + retrieval pipeline
├── llm.py          ← IBM watsonx Granite API client (IAM token, generation)
├── prompts.py      ← All LLM prompt templates
├── database.py     ← SQLite session logging
├── requirements.txt
├── data/           ← SQLite database (auto-created)
└── chroma_db/      ← ChromaDB persistent store (auto-created)
```

---

## Install dependencies

> Requires **Python 3.10+**

```bash
cd interview_trainer
pip install -r requirements.txt
```

If you want faster PDF extraction install PyMuPDF explicitly:
```bash
pip install PyMuPDF
```

---

## Run

```bash
cd interview_trainer
python app.py
```

Then open **http://localhost:7860** in your browser.

---

## How to use

| Step | Action |
|------|--------|
| 1 | Go to the **📁 Setup** tab |
| 2 | Upload your **Resume** (PDF / DOCX / TXT) |
| 3 | Upload the target **Job Description** (PDF / DOCX / TXT) |
| 4 | Choose maximum questions with the slider |
| 5 | Click **🚀 Start Interview** — documents are indexed into ChromaDB |
| 6 | Switch to the **💬 Interview** tab |
| 7 | Read the question, expand **"View Retrieved Context"** to see the grounding chunks |
| 8 | Type your answer and click **✅ Submit Answer** |
| 9 | Review your scores, strengths, gaps, and the ideal answer |
| 10 | Click **➡ Next Question** (or the agent asks a follow-up if you struggled) |
| 11 | When done, click **🏁 Finish & Generate Report** |
| 12 | View your full report in the **📊 Report** tab |
| 13 | All sessions are stored in `data/sessions.db` — browse them in the **🗂 History** tab |

---

## Configuration

Edit `llm.py` to change the model or credentials:

```python
MODEL_ID    = "ibm/granite-4-8b-instruct"
PROJECT_ID  = "3c18e38a-25fe-4845-82f4-ee1e4d5cafeb"
API_KEY     = "your-ibm-cloud-api-key"
WATSONX_URL = "https://us-south.ml.cloud.ibm.com/ml/v1/text/generation?version=2023-05-29"
```

---

## Features

- **RAG grounding** — every question and evaluation is anchored to actual chunks from your resume and JD stored in ChromaDB
- **Expandable context drawer** — see exactly which chunk, source, and chunk ID was used
- **Five-metric scoring** — Technical Accuracy, Relevance, Completeness, Clarity, Structure (each /10)
- **Adaptive follow-ups** — if you miss a core concept, the agent asks a targeted follow-up instead of moving on
- **Deterministic arithmetic** — overall scores are computed with Python arithmetic, not LLM guesses
- **Persistent history** — every session is logged to `data/sessions.db`
