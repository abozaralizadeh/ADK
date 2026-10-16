# Google ADK with Azure OpenAI — Python examples

This repo shows a minimal Python setup for Google's Agent Development Kit (ADK) using Azure OpenAI as the LLM backend. It mirrors the [Python Quickstart](https://google.github.io/adk-docs/get-started/python/) and extends it with an Azure-backed agent alongside the default Gemini sample.

## Repo layout
- `requirements.txt` — shared deps (`google-adk`, `litellm`).
- `my_adk_agent/agent.py` — quickstart-style Gemini agent with a mock `get_current_time` tool.
- `oai_multi_agent/agent.py` — Azure OpenAI-backed agent using `LiteLlm(model="azure/gpt-5.1-codex")`.
- `adk_yml_agent/root_agent.yaml` — YAML-defined agent example.

## Prerequisites
- Python 3.10+ and `pip`.
- An Azure OpenAI deployment name that matches the model string you pass to `LiteLlm` (e.g., `azure/gpt-5.1-codex`).
- API keys:
  - Gemini (if you run the Gemini sample) — `GOOGLE_API_KEY`.
  - Azure OpenAI — `AZURE_API_BASE`, `AZURE_API_KEY`, `AZURE_API_VERSION` (and any other variables your Azure deployment requires).

## Setup
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\\Scripts\\activate
pip install -r requirements.txt
```

Create a `.env` file (or export the variables) before running agents. For the Azure sample, start from the template: `cp oai_multi_agent/.env.example oai_multi_agent/.env`. Example:
```bash
AZURE_API_BASE="https://YOUR-RESOURCE.openai.azure.com/"
AZURE_API_KEY="YOUR-AZURE-KEY"
AZURE_API_VERSION="2025-04-01-preview"
# If needed: AZURE_API_DEPLOYMENT_NAME="gpt-5.1-codex"
```
Never commit real keys.

## Run the agents
- CLI chat (recommended during development):
  - `adk run my_adk_agent` for the Gemini sample.
  - `adk run oai_multi_agent` for the Azure OpenAI sample.
  - `adk run adk_yml_agent` for the YAML-defined agent.
- Web UI (for quick testing; development use only):
  - `adk web --port 8000` then pick the agent folder from the dropdown.

By default, ADK looks for `.env` in the agent folder; ensure the Azure variables are available to the shell or the `.env` file there.

## How the Azure OpenAI sample works
- Uses `google.adk.agents.llm_agent.Agent` with `LiteLlm` to route prompts to Azure OpenAI.
- The model string `azure/<deployment-name>` should match an active deployment in your Azure OpenAI resource.
- All other agent wiring (name, instruction, tools) follows the ADK Python quickstart pattern, so you can add tools or sub-agents the same way as in the Gemini example.

## Useful references
- ADK docs: https://google.github.io/adk-docs/get-started/python/
- ADK CLI (run/web) docs: see the “Run your agent” section in the quickstart above.
