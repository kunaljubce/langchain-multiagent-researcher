# Multi-Agent Researcher

A Streamlit research assistant that coordinates specialized LangChain agents to turn a topic into a structured research report. The application searches the web, extracts content from a relevant source, drafts a report, and sends the result through a critic for a quality review.

## Features

- Web research with Tavily, returning recent search results and source URLs.
- Targeted source reading with layered extraction using Trafilatura, Readability, and Beautiful Soup.
- Structured report generation with an introduction, key findings, conclusion, and sources.
- Critic feedback with a score, strengths, improvement areas, and a one-line verdict.
- Expandable raw search and reader outputs.
- Markdown report download from the Streamlit interface.

## How It Works

The research run follows four stages:

1. **Search Agent** finds recent, relevant information with the Tavily search tool.
2. **Reader Agent** selects a relevant URL and extracts readable page content.
3. **Writer Chain** combines the search results and extracted content into a report.
4. **Critic Chain** reviews the report and returns structured feedback.

The main orchestration lives in [`app.py`](app.py). Agent definitions are in [`src/agents/agents.py`](src/agents/agents.py), web tools are in [`src/tools/tools.py`](src/tools/tools.py), and a reusable command-line pipeline is available in [`src/pipeline/pipeline.py`](src/pipeline/pipeline.py).

## Requirements

- Python 3.10 or newer
- An OpenAI API key
- A Tavily API key
- [`uv`](https://docs.astral.sh/uv/) for the recommended environment and dependency workflow

The configured chat model is defined in [`src/agents/agents.py`](src/agents/agents.py). Update it there if your OpenAI account uses a different available model.

## Quick Start

From the project directory:

```bash
uv sync
```

Create a `.env` file in the project root:

```dotenv
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

Export the variables into the current shell before launching Streamlit:

```bash
set -a
source .env
set +a
```

Start the application:

```bash
uv run streamlit run app.py
```

Streamlit will print a local URL, normally `http://localhost:8501`. Enter a research topic, run the pipeline, and download the finished report as Markdown.

### Using an Existing Virtual Environment

If dependencies are already installed in an active environment, the equivalent command is:

```bash
streamlit run app.py
```

## Configuration

| Variable | Required | Purpose |
| --- | --- | --- |
| `OPENAI_API_KEY` | Yes | Authenticates the LangChain chat model used by the agents and chains. |
| `TAVILY_API_KEY` | Yes | Authenticates web search requests. |

Keep `.env` out of version control and never commit API keys. The application reads credentials from environment variables; the export step above makes values from `.env` available to the process.

## Project Structure

```text
.
├── app.py                  # Streamlit user interface and run orchestration
├── pyproject.toml          # Project metadata and dependencies
├── uv.lock                 # Locked dependency resolution
└── src/
	├── agents/agents.py    # Search/reader agents and writer/critic chains
	├── pipeline/pipeline.py # Reusable non-UI research pipeline
	└── tools/tools.py      # Tavily search and web page extraction tools
```

## Notes and Limitations

- Each run makes external requests to OpenAI, Tavily, and the selected web page, so network access and valid API credentials are required.
- The reader currently passes the first 800 characters of search output to the reader agent and returns up to 5,000 characters of extracted page content.
- Web pages may block automated requests or contain content that extraction libraries cannot reliably parse.
- Generated reports should be reviewed for accuracy and source quality before being used for consequential decisions.

## Development

Install the locked environment with:

```bash
uv sync
```

Run the Streamlit app locally with:

```bash
uv run streamlit run app.py
```

There is currently no automated test suite configured in this project. A practical manual check is to run the app with valid credentials and verify that all four pipeline stages complete and that the Markdown download contains the generated report.

## License

See [`LICENSE`](LICENSE) for the project license.
