# LM Studio Setup

LM Studio is a desktop app that downloads and runs AI models on your computer. It serves them as an OpenAI-compatible API that other apps (like OpenClaw or Hermes Agent) can use.

**Current version:** 0.4.20 (July 2026)

## Install

### Windows

Easiest: https://lmstudio.ai/download — download the `.exe` and run it.

Or via PowerShell:
```powershell
irm https://lmstudio.ai/install.ps1 | iex
```

### Linux

**GUI (AppImage):**
Download from https://lmstudio.ai/download — available for x64 and ARM64.
```bash
chmod +x LM_Studio-*.AppImage
./LM_Studio-*.AppImage
```

**Headless / Server (CLI only):**
```bash
curl -fsSL https://lmstudio.ai/install.sh | bash
```
This installs the `lms` CLI tool and `llmster` headless inference engine. No GUI required — ideal for servers.

### macOS

```bash
curl -L -o lm-studio.dmg "https://lmstudio.ai/download/macos"
open lm-studio.dmg
```

Drag to Applications. Supports both Apple Silicon (M1-M4) and Intel.

## First Time Setup

1. Open LM Studio
2. Go to "Discover" tab
3. Search for a model (see recommendations below)
4. Click download and wait
5. Go to "Developer" / Server tab
6. Pick the model from dropdown
7. Click "Start Server"
8. Wait for "Running on port 1234"

That's it. The API is up.

### CLI Server Start (headless)

```bash
lms server start --port 1234 --cors
```

Or run as a background daemon:
```bash
lms daemon up
```

## Recommended Models (July 2026)

| Model | Parameters | Good For | Min VRAM |
|---|---|---|---|
| Qwen 3.6 (27B Q4) | 27B | Best daily driver, coding, tool calling | 16 GB |
| Gemma 4 (12B) | 12B | Great generalist, fits most GPUs | 8 GB |
| Phi-4 Mini (3.8B) | 3.8B | Fast, low memory, still capable | 4 GB |
| DeepSeek R1 (14B Q4) | 14B | Reasoning & chain-of-thought | 12 GB |
| Llama 4 Scout | Various | Massive 10M token context window | 16+ GB |

Pick quantized (Q4/Q5 GGUF) versions for lower VRAM usage.

## Test It

```bash
curl http://127.0.0.1:1234/v1/models
```

Should return JSON with your model.

Or use the CLI:
```bash
lms chat
```

## API Endpoints

LM Studio is compatible with:
- `POST /v1/chat/completions` — Chat (OpenAI format)
- `POST /v1/completions` — Text completion
- `POST /v1/embeddings` — Vector embeddings
- `POST /v1/messages` — Anthropic format
- `GET /v1/models` — List loaded models

Also supports structured JSON output (`response_format`), native tool/function calling, and reasoning model parsing (`<think>` blocks).

## LM Studio Bionic

LM Studio Bionic is a separate agentic workspace app (launched mid-2026). It can inspect codebases, do multi-step debugging, manage documents, and includes offline voice input. It connects to your local LM Studio server via LM Link.

Bionic is optional — you don't need it for OpenClaw or Hermes Agent.

## Security

- **Default binding is `127.0.0.1`** (localhost only) — good
- **Don't change to `0.0.0.0`** unless you know what you're doing — it exposes the API to your entire LAN
- **Enable auth tokens** if serving on a network (Developer > Server Settings > Require Authentication)
- **CORS** — only enable when actively testing local web apps
- **Remote access** — use Tailscale or SSH tunnels, don't expose port 1234 to the internet

That's all. Move to OpenClaw or Hermes Agent when ready.
