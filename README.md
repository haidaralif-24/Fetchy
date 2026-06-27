# Synapse

A local, BYOK (bring-your-own-key) multi-agent research assistant. Breaks a topic into sub-questions, searches the web, summarizes findings, fact-checks claims against sources, corrects unsupported claims, and writes a final report — all on your own machine.

## Pipeline

Six agents run in sequence, each passing results to the next:

| Stage | Agent | What it does |
|-------|-------|-------------|
| 1 | **Planner** | Breaks your topic into sub-questions |
| 2 | **Search** | Gathers web content for each sub-question |
| 3 | **Summarizer** | Condenses search results into concise summaries |
| 4 | **Fact Checker** | Verifies claims against original sources |
| 5 | **Correction** | Revises unsupported or inaccurate claims |
| 6 | **Writer** | Produces the final markdown report |

## Requirements

- Python 3.11+
- An API key from one of the supported providers

## Setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Run

**GUI (desktop app):**
```bash
python -m app.main
```

**CLI (headless):**
```bash
python -m app.run "Your research topic"
```

On first run you'll be prompted to enter an API key and choose a provider.
You can also set environment variables for the CLI:

```bash
export SYNAPSE_API_KEY="sk-..."
export SYNAPSE_PROVIDER="openai"
export SYNAPSE_MODEL="gpt-4o"
python -m app.run "Your topic"
```

## Supported Providers

| Provider | Base URL | Default Model |
|----------|----------|---------------|
| OpenAI | `https://api.openai.com/v1` | `gpt-4o` |
| Groq | `https://api.groq.com/openai/v1` | `llama-3.3-70b-versatile` |
| Gemini | `https://generativelanguage.googleapis.com/v1beta/openai/` | `gemini-2.0-flash` |
| OpenRouter | `https://openrouter.ai/api/v1` | `openai/gpt-4o` |

## Search Backends

- **DuckDuckGo** — used by default, no API key required
- **Tavily** — optional; set `TAVILY_API_KEY` in config or environment

## Download pre-built executable

Pre-built binaries for macOS and Windows are available on the [Releases page](https://github.com/anomalyco/Synapse/releases). Download the latest version for your platform and run it directly — no Python setup required.

## Build from source

```bash
flet pack app/main.py --name synapse --icon assets/icon.png
```

Produces a standalone `dist/synapse` app bundle.

## Project Structure

```
app/
├── main.py                  # Flet entry point (GUI)
├── run.py                   # CLI entry point (headless)
├── config.py                # API key storage (keyring + JSON fallback)
├── graph/
│   ├── state.py             # Pydantic shared state
│   ├── build_graph.py       # LangGraph pipeline wiring
│   └── nodes/               # 6 pipeline agents
├── providers/
│   ├── llm_client.py        # OpenAI-compatible LLM wrapper
│   └── search/              # DuckDuckGo + Tavily
└── ui/                      # Flet views
    ├── key_setup_view.py
    ├── topic_view.py
    ├── progress_view.py
    └── report_view.py
```

## Tests

```bash
python -m pytest tests/
```
