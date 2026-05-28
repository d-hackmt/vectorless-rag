# Vectorless RAG

Ask questions about any PDF — no vector embeddings, no chunking.

Powered by [PageIndex](https://pageindex.ai/) + [Groq](https://console.groq.com/keys).

---

## What is RAG?

RAG = **Retrieval Augmented Generation**. You give an AI a document, ask a question, it finds the relevant parts and answers using them — instead of making things up.

---

## Traditional RAG vs Vectorless RAG

### Traditional RAG

```
PDF
 │
 ▼
Cut into chunks (every 500 words)     ← destroys document structure
 │
 ▼
Convert each chunk to a number vector ← "embeddings"
 │
 ▼
Query comes in
 │
 ▼
Find chunks with similar vectors      ← similarity ≠ relevance
 │
 ▼
Feed chunks to LLM → Answer
```

**Problems:**
- Cutting breaks context (a sentence in chunk 3 may need chunk 4 to make sense)
- Similarity search finds what *sounds* similar, not what *answers* the question
- No page numbers, no section context — hard to trace where the answer came from

---

### Vectorless RAG (this project)

```
PDF
 │
 ▼
PageIndex reads the document ONCE
 │
 ▼
Builds a tree (like a Table of Contents)

    Document
    ├── Abstract
    ├── Methodology
    │   ├── 2.1 Data Sources
    │   ├── 2.2 Study Selection
    │   └── 2.3 Data Extraction     ← each node has a title + page number
    ├── Results
    └── Conclusion

 │
 ▼
Query comes in
 │
 ▼
LLM looks at the tree and reasons:
"Section 2.3 is about data extraction — that's the answer"
 │
 ▼
Fetch only that section's full text
 │
 ▼
LLM answers with citations → "According to Section 2.3, page 8..."
```

**Benefits:**
- No embeddings needed
- Respects the document's natural structure
- Answers include exact section and page number
- Less noise = less hallucination

---

## Side-by-side Comparison

| | Traditional RAG | Vectorless RAG |
|---|---|---|
| **How it indexes** | Splits into fixed chunks | Builds a tree from document structure |
| **How it searches** | Vector similarity | LLM reasons over the tree |
| **Retrieval quality** | Finds similar text | Finds relevant sections |
| **Citations** | Chunk offset (not useful) | Section title + page number |
| **Setup complexity** | Needs an embedding model | Just upload the PDF |
| **Hallucination risk** | Higher (noisy chunks) | Lower (clean section text) |

---

## What We Learnt

1. **Chunking is a shortcut, not a solution.** Documents have structure — chapters, sections, subsections. Ignoring that structure loses context.

2. **An LLM can search, not just answer.** In this project, one LLM call decides *which sections to look at*. A second call *answers from those sections*. Both are simple prompts.

3. **Citations make answers trustworthy.** Because every retrieved node has a title and page number, the final answer can tell you exactly where the information came from.

4. **Less is more.** Instead of feeding the LLM 10 noisy chunks, we feed it 1–2 precise sections. The answers are cleaner.

---

## How to Run

**Install dependencies:**
```bash
pip install pageindex groq python-dotenv
```

**Create a `.env` file:**
```
GROQ_API_KEY=your_key_here
```

**Get your API keys:**
- Groq (free): https://console.groq.com/keys
- PageIndex: https://pageindex.ai/

**Run the notebook:** Open `vectorless_rag.ipynb` and run cells top to bottom.

---

## Feel free to connect

[Divesh Jadhwani](https://www.linkedin.com/in/dhackmt/)
