![CrewAI](https://miro.medium.com/v2/resize:fit:1400/0*-7HC-GJCxjn-Dm7i.png)

## 🏖️ VacAIgent — AI Trip Planner (CrewAI + Streamlit + FastAPI + CLI)

VacAIgent is an AI-powered trip planner built on top of CrewAI agents. It offers three interfaces:
- Streamlit web app for an interactive UI
- FastAPI service for programmatic access
- CLI for quick terminal usage

The system orchestrates multiple specialized agents (city selector, local expert, travel concierge) to research, plan, and return a complete itinerary.

---

### Features
- Multi-agent orchestration using CrewAI
- Choice of LLM backends (default: Gemini via `crewai[google-genai]`)
- Web UI (Streamlit), REST API (FastAPI), and CLI
- Built-in tools: web scraping via Browserless, Google Serper search, and a calculator tool

---

## Quickstart

1) Create a virtual environment and install dependencies

```bash
python -m venv myvenv
source myvenv/bin/activate
pip install -r requirements.txt
```

2) Set required environment variables

Copy the example env file and fill in your credentials:

```bash
cp env.example .env
```

3) Run one of the entrypoints

- Streamlit UI

```bash
streamlit run streamlit_app.py
```

- FastAPI server

```bash
uvicorn api_app:app --reload
# Visit http://localhost:8000/docs
```

- CLI

```bash
python cli_app.py \
  -o "Bangalore, India" \
  -d "Krabi, Thailand" \
  -s 2024-05-01 \
  -e 2024-05-10 \
  -i "2 adults who love swimming, dancing, hiking, shopping, food"
```

---

## Project Structure

```text
project/
  api_app.py           # FastAPI service
  cli_app.py           # Command-line interface
  streamlit_app.py     # Streamlit UI
  trip_agents.py       # Agent definitions and Stream-to-UI logging adapter
  trip_tasks.py        # Task prompts for agents
  tools/
    browser_tools.py   # Website scraping via Browserless + summarization
    search_tools.py    # Google Serper search tool
    calculator_tools.py# Simple math tool
  requirements.txt
```

---

## How it Works
- `trip_agents.py` defines three agents powered by the configured LLM.
- `trip_tasks.py` provides structured tasks for each agent.
- The runner (CLI/API/Streamlit) wires agents + tasks into a `Crew` and calls `crew.kickoff()` to produce the itinerary.

LLM selection is centralized through CrewAI’s `LLM` wrapper. Examples in code show switching between providers (Gemini/Groq/OpenAI/Ollama).

---

## Configuration Notes
- Default LLM in Streamlit and CLI is `gemini/gemini-2.0-flash` via `crewai[google-genai]`.
- The API example (`api_app.py`) uses OpenAI (commented Gemini in others). You can switch providers by adjusting the `LLM` or `Chat*` imports and credentials.

---

## Troubleshooting

- Streamlit “missing ScriptRunContext! This warning can be ignored when running in bare mode.”
  - This can appear when background threads attempt to update Streamlit. The app buffers worker logs and renders them from the main thread, but if you still see warnings in the terminal, they are harmless and won’t break the UI.

- Missing API keys
  - Ensure your `.env` contains the required keys and that your shell loaded them. The CLI/API perform validation and will tell you which are missing.

- Browserless/Serper quotas or errors
  - If scraping/search fails, the tools return error strings in results. Verify your tokens and quotas.

---

## Extending
- Add more tools under `tools/` and pass them into agents in `trip_agents.py`.
- Customize tasks in `trip_tasks.py` for different planning styles or outputs.
- Swap LLMs by changing the `LLM(model=...)` or using provider-specific clients.

Useful docs:
- CrewAI LLM connections: https://docs.crewai.com/how-to/llm-connections#connect-crewai-to-llms

---

## License
This project is provided as-is for educational and demonstration purposes. Review third‑party service terms (Browserless, Serper, LLM providers) before use.
