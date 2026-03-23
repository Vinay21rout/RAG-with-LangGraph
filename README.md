# RAG using LangGraph

A Retrieval-Augmented Generation (RAG) chatbot built with LangGraph, LangChain, and Groq LLM. It loads a PDF, chunks and embeds it, stores vectors in Chroma, and uses a LangGraph agent to answer questions from the document.

## Tech Stack

- **LLM**: Groq (llama-3.1-8b-instant)
- **Embeddings**: HuggingFace (all-MiniLM-L6-v2)
- **Vector Store**: Chroma
- **Framework**: LangGraph + LangChain
- **Tracing**: LangSmith
- **PDF Loader**: PyPDFLoader

## Project Structure

```
├── chatbot.ipynb       # Main notebook with RAG pipeline
├── main.py             # Entry point
├── data/               # Place your PDF files here
├── .env                # Environment variables (not committed)
├── requirements.txt    # Python dependencies
└── pyproject.toml      # Project metadata
```

## Setup

1. Clone the repo
   ```bash
   git clone <your-repo-url>
   cd rag-using-langgraph
   ```

2. Install dependencies
   ```bash
   pip install -r requirements.txt
   ```

3. Create a `.env` file
   ```env
   GROQ_API_KEY=<your_groq_api_key>
   LANGSMITH_API_KEY=<your_langsmith_api_key>
   LANGSMITH_TRACING=true
   LANGSMITH_PROJECT=RAG-using-LangGraph
   ```

4. Place your PDF inside the `data/` folder and update the path in `chatbot.ipynb`

5. Run the notebook
   ```bash
   jupyter notebook chatbot.ipynb
   ```

## How It Works

1. PDF is loaded and split into chunks
2. Chunks are embedded and stored in Chroma vector store
3. A `rag_tool` retrieves relevant chunks based on user query
4. LangGraph agent decides when to call the tool and generates a final answer

---

## Roadmap & Future Upgrades

### Multi-PDF Support with Context Filtering
- Load and index multiple PDFs into separate or tagged Chroma collections
- Agent selects the most relevant collection before retrieval, reducing noise and latency
- Metadata-based filtering (e.g. by document title, topic, date) to narrow search scope before vector similarity is computed

### Session & Conversation Memory
- Integrate LangGraph's `MemorySaver` checkpointer for persistent conversation history across turns
- Each session gets a unique `thread_id` so users can resume previous conversations
- Short-term memory (last N messages) + long-term memory (summarized history) for efficient context management

### Agent-Specific Context Selection
- Multiple specialized sub-agents, each scoped to a specific domain or document set (e.g. finance agent, legal agent, technical agent)
- A router agent analyzes the user query and delegates to the appropriate sub-agent
- Each sub-agent has its own retriever, tools, and system prompt tailored to its domain

### Tool Expansion
- **Web Search Tool**: Fall back to live web search (Tavily / SerpAPI) when the answer is not found in the PDF
- **Calculator Tool**: Handle numerical and mathematical queries
- **Code Interpreter Tool**: Execute and explain code snippets from technical documents
- **SQL Tool**: Query structured data sources alongside unstructured PDFs

### Reranking for Better Retrieval
- Add a cross-encoder reranker (e.g. `ms-marco-MiniLM`) after initial vector retrieval to reorder chunks by true relevance
- Reduces hallucination by ensuring the most accurate chunks reach the LLM

### Agentic Workflow Upgrades
- **Reflection node**: Agent self-evaluates its answer and retries if confidence is low
- **Plan-and-execute pattern**: Agent breaks complex queries into sub-tasks and solves them step by step
- **Human-in-the-loop**: Add interrupt points where a human can review or correct the agent before it proceeds

### UI & Deployment
- Build a Streamlit or Gradio frontend for a chat interface
- REST API via FastAPI for integration with external apps
- Dockerize the project for easy deployment
- Deploy on AWS (EC2 / Lambda + S3 for PDF storage)
