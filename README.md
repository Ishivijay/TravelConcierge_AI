# TravelConcierge AI

TravelConcierge AI is a multi-agent travel planning system powered by Google Gemini and the Agent Development Kit (ADK). It validates user inputs, orchestrates specialized agents in parallel, converts results to structured JSON, and presents a professional web interface for end-to-end trip planning.

## Features

- Multi-agent orchestration with parallel research (transport, accommodation, itinerary, food)
- Semantic input validation and field normalization
- Structured JSON outputs using Pydantic-style schemas
- Professional, responsive web UI with dark mode
- Built-in testing utilities and analytics

## Architecture

- Pipeline: User Input → Verifier → Propagator → Gate → Parallel Research → JSON Converters → Aggregator → Web UI
- Agents:
  - Travel: flights, trains, buses with pricing
  - Accommodation: hotels with ratings, amenities, and totals
  - Itinerary: day-by-day schedules with costs
  - Food: local cuisine and restaurant recommendations
- Standalone systems: run any agent independently for focused queries

## Guardrails and Safety

- Input validation: seven canonical fields (start/end locations, budget, duration, travel style, dietary preference, preferred currency)
- Constraint checks: reject vague or contradictory requests; require currency with budgets; normalize durations
- Output sanitization: markdown-to-HTML rendering with HTML sanitization to strip unsafe tags and attributes; preserves safe links
- Deterministic gating: the gate coordinator halts planning on validation errors and returns clear guidance
- Retry policy: exponential backoff for transient API errors (429/500/503/504) to reduce failure loops
- Session safety: isolate user sessions and avoid leaking prior context into new requests

## Quick Start

The notebook `travel_planner_agent.ipynb` contains the complete workflow.

### Prerequisites
- Python 3.10+
- Gemini API key set via `config.ini`
- Jupyter Notebook environment (VS Code, JupyterLab, or classic)

### Install Dependencies (Windows PowerShell)

```powershell
python -m pip install --upgrade pip
pip install google-adk google-genai flask markdown bleach
```

### Configure Authentication

Create or update `config.ini`:

```ini
[secrets]
GOOGLE_API_KEY=your_google_api_key_here
```

### Run the Notebook

1. Open `travel_planner_agent.ipynb` in VS Code/Jupyter.
2. Run cells in order (top to bottom): imports, config loading, agent creation, orchestration, web interface.
3. Launch the web interface in the final cell:

```python
start_web_server()
```

Access at `http://localhost:8050`.

## Usage

- Full Orchestrator: complete trip planning through the validation and aggregation pipeline.
- Standalone Agents: run Travel, Accommodation, or Itinerary agents individually.
- Example (programmatic):

```python
await travel_system.process_query(
    'Plan a 7-day trip from New York to Tokyo for 2 people. Budget: $3,500 USD. Mix of sightseeing and relaxation. Vegetarian meals.'
)
```

## Project Structure

```
config.ini                 # Gemini API key
README.md                  # Project documentation
travel_planner_agent.ipynb # Main multi-agent notebook
```

## Troubleshooting

- Missing or invalid API key: update `config.ini` with a valid `GOOGLE_API_KEY`.
- Import errors: ensure installs completed: `pip install google-adk google-genai flask markdown bleach`.
- Rate limits (429/500/503/504): built-in retry; re-run cell or slow request rate.
- Port in use: start server with another port, e.g. `start_web_server(port=8051)`.

## Implementation Highlights

- Parallel and sequential orchestration with in-memory runner
- Live Google Search tooling integration for up-to-date results
- Structured, client-ready outputs and responsive web UI
- Guardrails across validation, gating, and sanitized rendering
