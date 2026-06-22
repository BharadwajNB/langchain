# 🦜🔗 LangChain Learning Lab

A hands-on, notebook-based learning project exploring the core concepts of **LangChain v1** — from basic model invocations to advanced agent middleware. Each notebook builds on the previous, forming a progressive curriculum for mastering LangChain's modern API.

---

## 📌 What Is This Project?

This repository is a **practical tutorial series** designed to teach you how to:

- Connect to multiple LLM providers (OpenAI, Google Gemini, Groq) through LangChain's unified interface
- Build AI agents that can reason and use tools autonomously
- Structure LLM outputs into validated, typed Python objects
- Manage conversational memory with middleware for long-running agent sessions

The goal is to go from _"call an LLM and get text back"_ to _"build a production-aware agent with tools, structured output, and memory management"_ — all within Jupyter notebooks you can run and experiment with.

---

## 🗂️ Project Structure

```
langchain/
├── .env                         # API keys (GROQ, OPENAI, GOOGLE — not committed)
├── .gitignore                   # Excludes .venv, .env, __pycache__, etc.
├── .python-version              # Python 3.12
├── main.py                      # Minimal entry point (hello world)
├── pyproject.toml               # Project metadata & dependencies (uv-managed)
├── requirements.txt             # Pip-compatible dependency list
├── uv.lock                      # Deterministic lock file (uv package manager)
├── README.md                    # ← You are here
│
└── updatedlangchain/            # 📓 Core notebook curriculum
    ├── 1-langchainintor.ipynb    # Agents — Introduction & LangGraph
    ├── 2-modelintegration.ipynb  # Multi-provider model integration
    ├── 3-tools.ipynb             # Tool creation & binding
    ├── 4-messages.ipynb          # Message types & conversation design
    ├── 5-structuredoutput.ipynb  # Structured output (Pydantic, TypedDict, dataclass)
    └── 6-middlewares.ipynb       # Agent middleware (summarization, token management)
```

---

## 📓 Notebook Curriculum

### 1. `1-langchainintor.ipynb` — Introduction & Agents

**Purpose:** Get started with LangChain and build your first AI agent.

| Concept | What You Learn |
|---------|----------------|
| Version check | Verifying LangChain v1.3.9 installation |
| Environment setup | Loading API keys from `.env` using `python-dotenv` |
| `create_agent()` | Creating an agent with a model, tools, and a system prompt |
| LangGraph visualization | Viewing the agent's execution graph as a state machine diagram |
| Agent invocation | Asking the agent questions and observing tool call → response flow |

**Key Takeaway:** An agent is a _graph_ that decides when to call tools and when to respond directly. You define the model, tools, and system prompt — LangChain/LangGraph handles the reasoning loop.

**Model Used:** Groq (`llama-3.3-70b-versatile`)

---

### 2. `2-modelintegration.ipynb` — Multi-Provider Model Integration

**Purpose:** Learn how to swap between LLM providers using LangChain's unified `ChatModel` interface.

| Concept | What You Learn |
|---------|----------------|
| Google Gemini | Using `ChatGoogleGenerativeAI` with `gemini-2.5-flash` |
| Groq | Using `ChatGroq` with `llama-3.3-70b-versatile` |
| `.invoke()` | Synchronous single-prompt invocation |
| `.stream()` | Streaming responses chunk-by-chunk for real-time output |
| `.batch()` | Sending multiple prompts simultaneously for parallel processing |

**Key Takeaway:** LangChain abstracts away provider differences. The same `.invoke()` / `.stream()` / `.batch()` interface works regardless of whether you're using OpenAI, Gemini, or Groq.

**Models Used:** Google Gemini (`gemini-2.5-flash`), Groq (`llama-3.3-70b-versatile`)

---

### 3. `3-tools.ipynb` — Tool Creation & Binding

**Purpose:** Understand how to give LLMs the ability to call external functions (tools).

| Concept | What You Learn |
|---------|----------------|
| `@tool` decorator | Defining a Python function as a LangChain tool |
| Tool docstrings | How the LLM uses the docstring to understand what the tool does |
| `.bind_tools()` | Attaching tools to a model so it can decide when to invoke them |
| Tool call inspection | Reading `response.tool_calls` to see which tool was selected and with what arguments |

**Key Takeaway:** The LLM doesn't _execute_ the tool — it generates a structured request to call it. Your application is responsible for actually running the function and feeding the result back. This is the foundation of agentic behavior.

**Model Used:** Groq (`llama-3.3-70b-versatile`)

---

### 4. `4-messages.ipynb` — Message Types & Conversation Design

**Purpose:** Master the message system that drives all LangChain conversations.

| Concept | What You Learn |
|---------|----------------|
| `HumanMessage` | User input — what the human says |
| `SystemMessage` | Persona/behavior instructions for the LLM |
| `AIMessage` | Model responses (can also be injected manually for conversation history) |
| Text prompts vs. message prompts | The difference between `model.invoke("text")` and `model.invoke([messages])` |
| System prompt engineering | How detailed system messages dramatically change output quality |
| Conversation history | Building multi-turn conversations by composing message lists |

**Key Takeaway:** Messages are the fundamental unit of context. A well-crafted `SystemMessage` can turn a generic LLM into a domain expert. Manually injecting `AIMessage` objects lets you simulate or replay conversation history.

**Model Used:** Groq (`llama-3.3-70b-versatile`)

---

### 5. `5-structuredoutput.ipynb` — Structured Output

**Purpose:** Force LLMs to return data in validated, typed formats instead of free-form text.

| Concept | What You Learn |
|---------|----------------|
| Pydantic models | Defining schemas with `BaseModel` and `Field` for rich validation |
| `.with_structured_output()` | Binding a schema to the model so responses are automatically parsed |
| Nested structures | Using models-within-models (e.g., `MovieDetails` with `list[Actor]`) |
| `TypedDict` | Lightweight alternative for structured output without Pydantic |
| `@dataclass` | Using Python dataclasses as response schemas |
| Agent + `response_format` | Combining structured output with `create_agent()` for end-to-end extraction |

**Key Takeaway:** Structured output is critical for production systems. Instead of parsing free text, you get a validated Python object (e.g., `Movie(title='Inception', year=2010, director='Christopher Nolan', rating=8.5)`) that you can immediately use in your application.

**Models Used:** Groq (`llama-3.3-70b-versatile`), Google Gemini (`gemini-2.5-flash`)

---

### 6. `6-middlewares.ipynb` — Agent Middleware

**Purpose:** Learn how to control agent behavior with middleware — specifically, memory summarization for long-running conversations.

| Concept | What You Learn |
|---------|----------------|
| `SummarizationMiddleware` | Automatically summarizes conversation history when it exceeds a threshold |
| Message-based triggers | `trigger=("messages", 10)` — summarize after 10 messages accumulate |
| Token-based triggers | `trigger=("tokens", 550)` — summarize when token count exceeds 550 |
| `InMemorySaver` | Persistent in-memory checkpointing for stateful conversations |
| Thread configuration | Using `thread_id` to maintain separate conversation states |
| Tools + middleware | Combining tool use (hotel search) with automatic context summarization |

**Key Takeaway:** Without middleware, long conversations will exceed the model's context window and fail. `SummarizationMiddleware` solves this by compressing old messages into a summary, keeping only the most recent messages in full. This is essential for production agents that handle many turns.

**Model Used:** Google Gemini (`gemini-2.5-flash`)

---

## 🧰 Tech Stack & Dependencies

| Package | Purpose |
|---------|---------|
| `langchain` | Core framework — chains, agents, message types |
| `langchain-openai` | OpenAI model integration |
| `langchain-groq` | Groq model integration (fast inference for Llama models) |
| `langchain-google-genai` | Google Gemini model integration |
| `langchain-community` | Community-contributed integrations |
| `langgraph` | Agent execution graphs and stateful workflows |
| `python-dotenv` | Loads API keys from `.env` file |
| `ipykernel` | Jupyter notebook kernel support |
| `pydantic` | Data validation and structured output schemas |

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.12+**
- **uv** (recommended) or **pip** for package management
- API keys for at least one provider:
  - `GROQ_API_KEY` — [Get one free at groq.com](https://groq.com)
  - `GOOGLE_API_KEY` — [Google AI Studio](https://aistudio.google.com)
  - `OPENAI_API_KEY` — [OpenAI Platform](https://platform.openai.com)

### Setup

```bash
# 1. Clone or navigate to the project
cd langchain

# 2. Create your .env file with your API keys
#    (copy from the template below)
echo "GROQ_API_KEY=your_key_here" > .env
echo "OPENAI_API_KEY=your_key_here" >> .env
echo "GOOGLE_API_KEY=your_key_here" >> .env

# 3. Install dependencies
uv sync          # if using uv (recommended)
# OR
pip install -r requirements.txt

# 4. Open the notebooks
jupyter notebook updatedlangchain/
```

### Recommended Order

Follow the notebooks in numerical order — each one builds on concepts from the previous:

```
1-langchainintor.ipynb  →  2-modelintegration.ipynb  →  3-tools.ipynb
        ↓                                                     ↓
4-messages.ipynb  →  5-structuredoutput.ipynb  →  6-middlewares.ipynb
```

---

## 🎯 Why This Matters

This project demonstrates a progression from **simple LLM calls** to **production-grade agent architecture**:

1. **Model Integration** — Swap providers without changing application logic
2. **Tools** — Give LLMs the ability to take actions in the real world
3. **Messages** — Design conversations with precise control over context
4. **Structured Output** — Get validated, typed data instead of unpredictable text
5. **Middleware** — Manage memory and context for agents that run indefinitely

Together, these concepts form the foundation for building **AI-powered applications** — chatbots, data extraction pipelines, autonomous agents, and more.

---

## ⚠️ Notes

- The `.env` file is **gitignored** — you must create your own with valid API keys
- Notebook 6 has a known minor bug: `m.contents` should be `m.content` in the `count_tokens` function (typo — `contents` vs `content`)
- All notebooks use LangChain **v1.3.9** with the latest `create_agent()` API

---

## 📄 License

This is a personal learning project. Use freely for educational purposes.
