# Hermes Agent Setup

Hermes Agent is an open-source, local-first AI agent runner by Nous Research. It can connect to any local LLM, execute code, automate tasks, and continuously improve itself by creating reusable skills.

**Current version:** v0.19.0 (Quicksilver Release, July 2026)

## Hermes vs OpenClaw (Quick Difference)

- **OpenClaw**: Background daemon, messaging app integrations, browser-based UI
- **Hermes Agent**: Terminal-first, self-improving skills, MCP native support
- Both can point to **LM Studio** or **Ollama** so your model stays local.

## Before Starting

You need:
- LM Studio or Ollama running with a model loaded
- The server active (`http://127.0.0.1:1234` for LM Studio, `http://127.0.0.1:11434` for Ollama)

## Install Hermes Agent

**Linux / macOS / WSL2:**
```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

**Windows (PowerShell):**
```powershell
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

**Update existing install:**
```bash
hermes update
```

## Configure for LM Studio

Run the interactive model picker:
```bash
hermes model
```

Select **Custom Endpoint / Local Model** and enter:
- URL: `http://localhost:1234/v1`
- Model: the model ID loaded in LM Studio

Or for Ollama:
- URL: `http://localhost:11434`

### Config files

Hermes stores config in:
- Settings: `~/.hermes/config.yaml`
- Secrets: `~/.hermes/.env`

Example `config.yaml`:
```yaml
default_model: qwen-3.6-27b
default_provider: local
temperature: 0.2
max_tokens: 4096

providers:
  local:
    type: openai-compatible
    base_url: http://127.0.0.1:1234/v1
    api_key: lm-studio

mcp_servers:
  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "${GITHUB_TOKEN}"
```

## MCP (Model Context Protocol) Support

Hermes Agent has native MCP support. Manage MCP servers with:

```bash
hermes mcp              # Interactive MCP server manager
hermes mcp catalog      # Browse available MCP servers
```

During a session, reload MCP tools with `/reload-mcp`.

Tools are automatically prefixed with server names (e.g., `mcp_github_create_issue`) for clarity.

## Pick the Right Model

### Local models (on your GPU)

| Model | Parameters | Good For |
|---|---|---|
| Nous Hermes 4 (36B/70B) | 36-70B | Best tool calling, built for Hermes |
| Qwen 3.x (14B/32B) | 14-32B | Best all-around for mid-range GPUs |
| DeepSeek R1 | Various | Step-by-step reasoning |

### Cloud models (via API)

| Model | Good For |
|---|---|
| Claude 3.5/3.7 Sonnet | Gold standard for agentic coding |
| DeepSeek V3/R1 (OpenRouter) | Best cost-to-performance ratio |
| Gemini 2.0/3 Flash | High-speed, cheap sub-agent tasks |

Tips:
- Start with 7B-14B models if your machine can handle them
- Use Q4/Q5 quantized versions for lower VRAM
- Keep `temperature` low (0.1-0.3) for tool-heavy tasks
- Set context length to 64k+ tokens for reliable tool calling

## Run Hermes Agent

```bash
hermes
```

That's it. Opens the interactive TUI.

Or start the web UI:
```bash
hermes --web
```

## Self-Improving Skills

Hermes automatically creates reusable skill files when it solves tasks. These are saved locally and loaded in future sessions, so it gets better over time without any cloud training.

## Verify Setup

```bash
hermes doctor
```

Checks LLM connectivity, tool availability, and environment health.

### Quick test

```bash
hermes prompt "Reply with exactly: HERMES_OK"
```

Expected output: `HERMES_OK`

## Troubleshooting

**Cannot connect to LM Studio:**
- Check LM Studio Server tab is running
- Confirm URL is `http://127.0.0.1:1234/v1`
- Run `curl http://127.0.0.1:1234/v1/models`

**Model not found:**
- Load the model in LM Studio first
- Check model ID matches with `hermes model`

**Slow responses:**
- Switch to a smaller model
- Lower `max_tokens`
- Close other GPU/CPU heavy apps

Done. Hermes is now running locally with LM Studio.
