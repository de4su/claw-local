# OpenClaw Setup

OpenClaw is an AI agent framework. You point it at a local model (like LM Studio) and it can automate tasks on your computer — click things, run commands, manage files, whatever you teach it to do.

**Current version:** v2026.7.x

## Before Starting

You need:
- LM Studio running with a model loaded
- LM Studio's Server active on port 1234

## Install

**Recommended (one-liner):**
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
openclaw onboard
```

**Docker:**
```bash
git clone https://github.com/openclaw/openclaw
cd openclaw
./scripts/docker/setup.sh
```

Or with pre-built image:
```bash
export OPENCLAW_IMAGE="ghcr.io/openclaw/openclaw:latest"
./scripts/docker/setup.sh
```

**From source (pnpm):**
```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
corepack enable
pnpm install
pnpm openclaw setup
```

## Configure

The easiest way is the interactive onboard command:
```bash
openclaw onboard
```

Select LM Studio as your provider, enter your base URL (`http://127.0.0.1:1234/v1`), and pick a model.

### Non-interactive setup

```bash
openclaw onboard \
  --non-interactive \
  --auth-choice lmstudio \
  --custom-base-url http://localhost:1234/v1 \
  --lmstudio-api-key "lm-studio" \
  --custom-model-id lmstudio/qwen-3.6-27b
```

### Manual config

OpenClaw reads from `~/.openclaw/openclaw.json` (Linux/Mac) or `%USERPROFILE%\.openclaw\openclaw.json` (Windows).

```json
{
  "gateway": {
    "mode": "local",
    "auth": {
      "mode": "token",
      "token": "make-up-a-password"
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "lmstudio/qwen-3.6-27b"
      }
    }
  },
  "models": {
    "providers": {
      "lmstudio": {
        "baseUrl": "http://127.0.0.1:1234/v1",
        "apiKey": "lm-studio",
        "api": "openai-completions"
      }
    }
  }
}
```

Key parts:
- `mode: local` — no cloud
- `baseUrl` — where LM Studio is
- `token` — just pick something, used for auth to the gateway

## Run It

```bash
openclaw gateway --port 18789
```

Should say it's listening on 18789 and connected to LM Studio.

Open `http://localhost:18789`.

## Built-in Tools

```bash
openclaw doctor          # Diagnose issues
openclaw security audit  # Check for vulnerabilities
openclaw security audit --fix  # Auto-fix security issues
openclaw config set <key> <value>  # Change settings
```

## Messaging Integrations

OpenClaw now has built-in channel support — no separate bot scripts needed:

```bash
openclaw channels add --channel discord
openclaw channels add --channel telegram
openclaw channels login --channel whatsapp
```

See the individual connection guides for details.
