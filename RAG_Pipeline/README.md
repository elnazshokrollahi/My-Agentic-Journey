# 🔎 RAG Pipeline — Support Ticket Q&A

A retrieval-augmented generation (RAG) system that answers questions from a support-ticket knowledge base — retrieving the most relevant tickets and answering **only** from them, with citations.

Built in Google Colab as the Module 4 exercises for an applied LLM course.

---

## What it does

Ask a plain-English question (e.g. *"How do I fix database timeouts?"*), and the system:

1. **Retrieves** the most relevant support tickets by semantic similarity
2. **Injects** them into the prompt as context
3. **Generates** a grounded answer with ticket citations — or refuses if nothing relevant is found

## Tech stack

- **Python** + **Google Colab**
- **LangChain** — retrievers, prompt templates, and LCEL chains
- **Chroma** — vector store for similarity search
- **OpenAI** — `text-embedding-3-small` for embeddings, `gpt-4o-mini` for generation

## Exercises explored

Each exercise changes one part of the pipeline and observes the effect:

1. **Prompt templates** — how wording shapes the answer
2. **Retrieval tuning** — how `k` and MMR (relevance vs. diversity) change what's retrieved
3. **Inline citations** — forcing `[TICKET-XXX]` tags to prove groundedness
4. **Fallback system** — using retrieval distance to refuse low-confidence questions
5. **Chain strategies** — `stuff` vs. `map-reduce` (speed, cost, and quality trade-offs)
6. **Metadata filtering** — combining semantic search with exact category/priority filters
7. **Streaming** — token-by-token responses for better perceived speed
8. **Multi-turn conversation** — chat history so the assistant remembers context across questions

## How to run

1. Open the notebook in [Google Colab](https://colab.research.google.com).
2. Add your OpenAI API key as a Colab secret named `OPENAI_APIKEY` (🔑 panel, enable notebook access).
3. Put the ticket data (`synthetic_tickets.json`) in your Google Drive and mount it, or point the loader at your own file.
4. Run the cells top to bottom (**Runtime → Run all** avoids run-order errors).

> Tip: for a throwaway notebook, build the Chroma store **in memory** (skip `persist_directory`) to avoid file-lock and duplicate-document issues.

## Key takeaways

- RAG keeps a model grounded by **retrieving real text** and answering from it, not from general knowledge.
- A trustworthy system needs a **confidence gate** — retrieval always returns *something*, so weak matches must be caught.
- Threshold and `k` values aren't universal — they're **calibrated to your own data**.
