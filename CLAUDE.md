# CLAUDE.md

Guidance for Claude Code (and humans) working in this repository. User-facing setup lives in
[README.md](README.md); don't duplicate it here.

## What this is

Minimal Python examples for Google's Agent Development Kit (ADK). Three independent agents sit side
by side, each in its own folder that `adk run` / `adk web` can load:

- a Gemini agent from the ADK quickstart, with a mock tool,
- the same agent routed to Azure OpenAI through LiteLLM,
- an agent defined only in YAML.

There is no application code beyond the agent definitions, and no tests.

## Commands

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt              # google-adk, litellm (unpinned)
adk run my_adk_agent                         # Gemini sample (needs GOOGLE_API_KEY)
adk run oai_multi_agent                      # Azure OpenAI sample (needs the AZURE_* variables)
adk run adk_yml_agent                        # YAML-defined agent (Gemini)
adk web --port 8000                          # dev UI, pick the agent folder from the dropdown
```

## Layout

```
my_adk_agent/      agent.py: root_agent on gemini-2.5-flash + mock get_current_time tool
oai_multi_agent/   agent.py: root_agent on LiteLlm("azure/gpt-5.1-codex"); .env.example lists AZURE_* names
adk_yml_agent/     root_agent.yaml: LlmAgent on gemini-2.5-flash, no tools or sub-agents
  tmp/             an identical copy of root_agent.yaml (left by the ADK web builder); edit the outer file
requirements.txt   shared dependencies
```

## Configuration

- Gemini: `GOOGLE_API_KEY`.
- Azure OpenAI (read by LiteLLM): `AZURE_API_BASE`, `AZURE_API_KEY`, `AZURE_API_VERSION`.
- ADK loads `.env` from the agent's folder, so the Azure sample reads `oai_multi_agent/.env`
  (git-ignored, along with `.adk/`, `.vscode/` and `__pycache__/`). Never commit real keys.

## Conventions / gotchas

- Each agent package must expose a module-level `root_agent`, and its `__init__.py` must import
  `agent`; that is how the ADK CLI finds it. A new example = a new folder with both files.
- The Azure model string is `azure/<deployment-name>`: it has to match a deployment that exists in
  the Azure OpenAI resource, not a model family name.
- Tools are plain Python functions passed in `tools=[...]`; the docstring and type hints are what
  the model sees. `get_current_time` returns a fixed time on purpose (it's a mock).
- Dependencies are unpinned, so ADK API changes land on the next install. If an import path breaks
  (e.g. `google.adk.agents.llm_agent`), check the ADK release notes before rewriting the samples.
