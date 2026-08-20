# Corrective RAG

An executable progression from basic retrieval-augmented generation to a corrective RAG workflow built with LangChain, LangGraph, FAISS, and optional Tavily web search.

## What this project teaches

The notebooks build the same question-answering system in deliberate steps:

| Notebook | Focus | What changes |
| --- | --- | --- |
| `1_basic_rag.ipynb` | Baseline RAG | Retrieve PDF chunks and generate an answer from context. |
| `2_retrieval_refinement.ipynb` | Retrieval refinement | Decompose retrieved content and keep useful evidence. |
| `3_retrieval_evaluator.ipynb` | Retrieval evaluation | Score evidence and route weak retrieval for correction. |
| `4_web_search_refinement.ipynb` | Web fallback | Bring in current information when the local corpus is insufficient. |
| `5_query_rewrite.ipynb` | Query rewriting | Convert the original question into a search-ready web query. |
| `6_ambiguous.ipynb` | Ambiguity-aware routing | Detect unclear questions and avoid presenting an overconfident answer. |

The final notebook is the best place to see the complete corrective loop. The earlier notebooks are intentionally kept as small, inspectable milestones.

## Quick start

### 1. Create an environment

```bash
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the project dependencies:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m ipykernel install --user --name corrective-rag --display-name "Python (corrective-rag)"
```

### 2. Configure API keys

Copy `.env.example` to `.env` and add an OpenAI key. Notebooks 4-6 also require a Tavily key for web search.

```text
OPENAI_API_KEY=your_openai_key
TAVILY_API_KEY=your_tavily_key
```

Never commit `.env` or API keys.

### 3. Run the notebooks

Open the repository folder in VS Code or Jupyter, select the `Python (corrective-rag)` kernel, and run the notebooks from top to bottom. Start with `1_basic_rag.ipynb` and continue in order. The notebooks load `documents/book1.pdf`, `documents/book2.pdf`, and `documents/book3.pdf`, build a fresh FAISS index, and call the configured models.

## Architecture

```text
Question
   |
   v
Retrieve local PDF chunks ----> Evaluate evidence
                                      |
                         +------------+------------+
                         |                         |
                    Useful context            Weak or unclear
                         |                         |
                         v                         v
                      Generate       Rewrite query -> Tavily web search
                                                           |
                                                           v
                                                Refine context -> Generate
```

Each notebook exposes the graph state and routing nodes directly, making it easy to inspect what was retrieved, why a correction path was chosen, and what evidence reached generation.

## Configuration notes

- The default chat model is `gpt-4o-mini`.
- The default embedding model is `text-embedding-3-large`.
- Local retrieval uses FAISS with four candidate chunks.
- Web search is only used in the notebooks that import `TavilySearchResults`.
- The PDFs are reference material for learning. Replace them with your own corpus by updating the loader paths in the notebooks.

## Project layout

```text
.
├── 1_basic_rag.ipynb
├── 2_retrieval_refinement.ipynb
├── 3_retrieval_evaluator.ipynb
├── 4_web_search_refinement.ipynb
├── 5_query_rewrite.ipynb
├── 6_ambiguous.ipynb
├── documents/
│   ├── book1.pdf
│   ├── book2.pdf
│   └── book3.pdf
├── .env.example
├── requirements.txt
└── README.md
```

## Responsible use

Corrective routing improves evidence selection; it does not make generated answers automatically true. Inspect retrieved sources, keep web search enabled only when appropriate, and evaluate answers against trusted references before using this pattern in production.

## License

Add the license that applies to the source material and your intended use before publishing a deployment built from this project.
