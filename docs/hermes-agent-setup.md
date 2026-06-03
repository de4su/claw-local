# Hermes Agent Setup

Hermes Agent is a local-first AI agent runner. Like OpenClaw, it can connect to an LLM and perform real tasks, but its config and runtime commands are Hermes-specific.

## Hermes vs OpenClaw (Quick Difference)

- **OpenClaw**: Uses `~/.openclaw/openclaw.json` and `openclaw gateway ...`
- **Hermes Agent**: Uses `~/.hermes-agent/hermes-agent.json` and `hermes-agent start ...`
- Both can point to **LM Studio** so your model stays local.

## Before Starting

You need:
- Node.js installed
- LM Studio already running
- A model loaded in LM Studio's **Server** tab (`http://127.0.0.1:1234`)

## Install Hermes Agent

### Option A: Global install (quickest)

```bash
npm install -g hermes-agent
```

Done.

### Option B: Local/development install from source

```bash
git clone https://github.com/<your-org>/hermes-agent.git
cd hermes-agent
npm install
npm run build
npm link
```

Now `hermes-agent` is available in your shell from your local source checkout.

## Configure Hermes for LM Studio

Hermes reads config from:
- Linux/macOS: `~/.hermes-agent/hermes-agent.json`
- Windows: `%USERPROFILE%\\.hermes-agent\\hermes-agent.json`

Create/edit it:

```bash
mkdir -p ~/.hermes-agent
nano ~/.hermes-agent/hermes-agent.json
```

Paste this starter config:

```json
{
  "server": {
    "host": "127.0.0.1",
    "port": 3000
  },
  "llm": {
    "provider": "openai-compatible",
    "baseUrl": "http://127.0.0.1:1234/v1",
    "apiKey": "lm-studio",
    "model": "qwen2.5-7b-instruct"
  },
  "agent": {
    "name": "Hermes Local",
    "temperature": 0.2,
    "maxTokens": 2048
  },
  "tools": {
    "shell": true,
    "filesystem": true
  }
}
```

Key parts:
- `baseUrl`: where LM Studio serves OpenAI-compatible API
- `apiKey`: LM Studio accepts any string
- `model`: must match the model loaded in LM Studio

## Pick the Right Model for Hermes

For Hermes tasks, use an **instruction-tuned** model first.

Good local starting points:
- `qwen2.5-7b-instruct`
- `phi-4-mini-instruct`
- `llama-3.1-8b-instruct`

Tips:
- Start with 7B/8B models if your machine can handle them
- Use 4-bit quantized versions for lower RAM/VRAM
- Keep `temperature` low (`0.1` to `0.3`) for tool-heavy tasks

To change model, update only this line in `hermes-agent.json`:

```json
"model": "your-loaded-model-id"
```

## Run Hermes Agent Locally

If installed globally:

```bash
hermes-agent start --config ~/.hermes-agent/hermes-agent.json
```

If installed locally/in development:

```bash
npx hermes-agent start --config ~/.hermes-agent/hermes-agent.json
```

You should see Hermes start and connect to LM Studio.

## Test the Setup

### 1) Confirm LM Studio API

```bash
curl http://127.0.0.1:1234/v1/models
```

You should get JSON with your loaded model.

### 2) Send a quick Hermes prompt

```bash
hermes-agent prompt "Reply with exactly: HERMES_OK"
```

Expected output: `HERMES_OK`

### 3) Basic tool-use sanity check

```bash
hermes-agent prompt "What is 2 + 2? Reply with one number."
```

Expected output: `4`

## Troubleshooting

### Hermes cannot connect to LM Studio
- Check LM Studio Server tab is running
- Confirm URL is exactly `http://127.0.0.1:1234/v1`
- Try `curl http://127.0.0.1:1234/v1/models`

### Model not found
- Load that exact model in LM Studio first
- Ensure `"model"` value matches model ID from `/v1/models`

### Command not found: `hermes-agent`
- Reopen terminal after global install
- Or run with `npx hermes-agent ...`
- If using source install, rerun `npm link`

### Slow responses
- Switch to a smaller model
- Lower `maxTokens`
- Close other GPU/CPU heavy apps

### Port already in use
- Change Hermes `server.port` in config (for example, `3001`)
- Restart Hermes Agent

Done. Hermes is now running locally with LM Studio.
