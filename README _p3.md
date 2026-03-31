# YouTube Tool-Calling Agent with LangChain

A Python project that builds an AI agent capable of interacting with YouTube programmatically using **tool calling** — a technique where a Large Language Model (LLM) autonomously decides which external functions to invoke in order to answer a query.

The agent is built with **LangChain** and powered by **OpenAI GPT-4o-mini**. It progresses through three levels of complexity: manual tool calling, automated chains, and a fully recursive agent loop.

---

## Demo

```
Query: "Summarize this video and tell me its stats: https://www.youtube.com/watch?v=T-D1OfcDW1M"

Agent flow:
  1. LLM → calls extract_video_id("https://www.youtube.com/watch?v=T-D1OfcDW1M")
  2. LLM → calls fetch_transcript("T-D1OfcDW1M")
  3. LLM → generates final summary with stats
```

---

## Features

- **5 custom tools** registered with LangChain's `@tool` decorator
- **Manual tool calling** — step-by-step loop to understand the mechanics
- **Summarization chain** — fixed pipeline using `RunnablePassthrough` and the `|` operator
- **Recursive agent** — dynamic loop that runs until the LLM produces a final answer, calling any number of tools in any order
- Handles all YouTube URL formats (standard, shortened, embedded)

---

## Tools

| Tool | Input | Output |
|---|---|---|
| `extract_video_id` | YouTube URL | 11-character video ID |
| `fetch_transcript` | Video ID | Full transcript as string |
| `search_youtube` | Search query | List of matching videos with URLs |
| `get_full_metadata` | YouTube URL | Title, views, likes, duration, channel, chapters |
| `get_thumbnails` | YouTube URL | List of all thumbnail URLs |

---

## Project Structure

```
youtube-tool-calling-agent/
├── YouTube_Tool_Calling_Agent.ipynb   # Main notebook
├── requirements.txt                    # Dependencies
├── .gitignore                          # Ignores .env and checkpoints
└── README.md                           # This file
```

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/yassine589/youtube-tool-calling-agent.git
cd youtube-tool-calling-agent
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Set your OpenAI API key

Create a `.env` file at the project root:

```
OPENAI_API_KEY=your-api-key-here
```

Or set it directly in the notebook:

```python
import os
os.environ["OPENAI_API_KEY"] = "your-api-key-here"
```

### 4. Run the notebook

```bash
jupyter notebook YouTube_Tool_Calling_Agent.ipynb
```

---

## How Tool Calling Works

When the LLM is invoked with tools bound, it does not always respond with text. It may instead emit a **tool call** — a structured JSON request specifying which function to run and with what arguments. Your code executes the function and returns the result as a `ToolMessage`. The LLM then continues reasoning from there.

```
User query
    │
    ▼
LLM (with tools bound)
    │
    ├── tool_calls present? ──► execute tools ──► feed results back ──► loop
    │
    └── no tool_calls ──► final text answer
```

The **recursive agent** (`universal_chain`) automates this loop entirely — it keeps iterating until `should_continue()` returns `False`, meaning the LLM has produced its final answer.

---

## Tech Stack

- [LangChain](https://python.langchain.com/) `0.3.23`
- [langchain-openai](https://pypi.org/project/langchain-openai/) `0.3.14`
- [langchain-community](https://pypi.org/project/langchain-community/) `0.3.16`
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — metadata extraction
- [youtube-transcript-api](https://github.com/jdepoix/youtube-transcript-api) `1.1.0`
- [pytube](https://pytube.io/) — YouTube search
- OpenAI GPT-4o-mini

---

## License

MIT
