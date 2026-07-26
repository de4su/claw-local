# Local LLM + OpenClaw

Your private AI agent that remembers conversations without leaking data.

Run AI models locally with LM Studio and give them real capabilities with OpenClaw or Hermes Agent. Your AI can control your mouse, execute commands, access files, and automate tasks — all without sending a single byte to the cloud.

## What You're Setting Up

- **LM Studio** (v0.4.20) - Desktop app that runs AI models locally and serves them over an OpenAI-compatible API
- **OpenClaw** (v2026.7.x) - AI agent framework that can automate tasks on your computer using local models
- **Hermes Agent** (v0.19.0) - Alternative local-first agent runner by Nous Research, with self-improving skills

## Why This Setup?

You must've heard about OpenClaw, instead of using it with a company owned LLM on cloud, consider a more privacy focused option.

🔒 **Complete privacy** — Everything runs on your machine

🧠 **Persistent memory** — Your AI remembers past conversations

⚡ **Real actions** — Not just chat, but actual task execution

🛡️ **Security audits** — Built-in tools to harden your setup

## Quick Start

### 1. Get LM Studio Running

**Windows:**
```powershell
irm https://lmstudio.ai/install.ps1 | iex
```
Or download from https://lmstudio.ai/download and run the installer.

**Linux:**
```bash
curl -fsSL https://lmstudio.ai/install.sh | bash
```
This installs the `lms` CLI and the `llmster` headless inference engine. For the GUI, download the AppImage from https://lmstudio.ai/download.

**Mac:**
```bash
curl -L -o lm-studio.dmg "https://lmstudio.ai/download/macos"
open lm-studio.dmg
```

Start the server:
```bash
lms server start --port 1234 --cors
```
Or use the GUI: go to Developer/Server tab → Start Server.

LM Studio serves models on `localhost:1234`.

### 2. Get OpenClaw

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
openclaw onboard
```

The `onboard` command walks you through setup interactively — pick LM Studio as your provider, enter `http://127.0.0.1:1234/v1` as the base URL, and select your loaded model.

### 3. Start Everything

Make sure LM Studio's Server is running, then:

```bash
openclaw gateway --port 18789
```

Open `http://localhost:18789`. Done.

### 4. Harden Your Setup

```bash
openclaw security audit
openclaw security audit --fix
```

This checks for known vulnerabilities and applies recommended fixes.

## Recommended Local Models (July 2026)

| Model | Size | Best For | VRAM Needed |
|---|---|---|---|
| Qwen 3.x (27B) | 27B | Daily driver, coding, agentic work | 16-24 GB |
| Gemma 4 (12B) | 12B | Great generalist, creative writing | 8-16 GB |
| Phi-4 Mini (3.8B) | 3.8B | Low memory, fast responses | 4 GB |
| DeepSeek R1 | 1.5-32B | Chain-of-thought reasoning | 8-24 GB |
| Llama 4 Scout | Various | Massive context window (10M tokens) | 16+ GB |

## Alternatives Worth Knowing

| Tool | What It Does | Role |
|---|---|---|
| [Ollama](https://ollama.ai) | Headless CLI model server | LM Studio alternative (no GUI) |
| [Jan.ai](https://jan.ai) | Privacy-first desktop chat UI | LM Studio alternative (with GUI) |
| [Open Interpreter](https://github.com/OpenInterpreter/open-interpreter) | Terminal code execution agent | Interactive coding agent |
| [Aider](https://aider.chat) | Git-integrated coding agent | Pair programming tool |
| [AnythingLLM](https://anythingllm.com) | Desktop RAG & knowledge base | Document QA & chat |

All of these can use the same local LM Studio or Ollama backend.

## Full Guides

- [LM Studio Setup](docs/lm-studio-setup.md)
- [OpenClaw Setup](docs/openclaw-setup.md)
- [Hermes Agent Setup](docs/hermes-agent-setup.md)
- [Connect to Telegram](docs/connect-telegram.md)
- [Connect to Discord](docs/connect-discord.md)
- [Connect to WhatsApp](docs/connect-whatsapp.md)
- [Security Guide](docs/security.md)

## ⚠️ Security Notice

OpenClaw runs with high system privileges (shell, filesystem, credentials). Several critical CVEs were disclosed in 2026. Always:

1. Run `openclaw security audit --fix` after installation
2. Bind the gateway to `127.0.0.1` only
3. Use strong auth tokens
4. Run in a container or VM if possible
5. Keep OpenClaw updated

See [Security Guide](docs/security.md) for full details.

Done. Enjoy your private AI. 🔒
