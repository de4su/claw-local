# Local LLM + OpenClaw

Want AI that stays on your computer? This is how you set it up.

LM Studio downloads and runs AI models on your machine. OpenClaw lets those models actually do stuff - control your mouse, run commands, access files, etc. No data leaves your computer.

## What You're Setting Up

- **LM Studio** - Desktop app that runs AI models locally and serves them over an API
- **OpenClaw** - AI agent framework that can automate tasks on your computer using local models
- **Result** - AI that actually does things, stays private, costs nothing

## Why

Your data never leaves your machine. No cloud, no tracking, no monthly bills. Your AI assistant can actually do stuff on your computer.

## Quick Start

### 1. Get LM Studio Running

**Windows:** Download from https://lmstudio.ai/download and run the installer. Download a model from the app, start the server.

**Linux:**
```bash
curl -L -o lm-studio.AppImage "https://github.com/lmstudio-ai/lmstudio/releases/download/0.3.3/LM_Studio-0.3.3-x64.AppImage"
chmod +x lm-studio.AppImage
./lm-studio.AppImage
```

**Mac:**
```bash
curl -L -o lm-studio.dmg "https://lmstudio.ai/download/macos"
open lm-studio.dmg
```

LM Studio serves models on `localhost:1234`.

### 2. Get OpenClaw

```bash
npm install -g openclaw
```

### 3. Configure OpenClaw

Edit `~/.openclaw/openclaw.json`:

```json
{
  "gateway": {
    "mode": "local",
    "auth": {
      "mode": "token",
      "token": "pick-a-password"
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "lmstudio/phi-4-mini-instruct"
      },
      "models": {
        "phi-4-mini-instruct": {
          "alias": "Phi 4 Mini Instruct"
        }
      }
    }
  },
  "models": {
    "providers": {
      "lmstudio": {
        "baseUrl": "http://127.0.0.1:1234/v1",
        "apiKey": "lm-studio",
        "api": "openai-completions",
        "models": [
          {
            "id": "phi-4-mini-instruct",
            "name": "Phi 4 Mini Instruct",
            "reasoning": false,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 20256,
            "maxTokens": 4096
          }
        ]
      }
    }
  }
}
```

### 4. Start Everything

Make sure LM Studio's Server shows "Running", then:

```bash
npx openclaw gateway --port 18789
```

Open `http://localhost:18789`. Done.

## Full Guides

- [LM Studio Setup](docs/lm-studio-setup.md)
- [OpenClaw Setup](docs/openclaw-setup.md)
- [Connect to Telegram](docs/connect-telegram.md)
- [Connect to Discord](docs/connect-discord.md)
- [Connect to WhatsApp](docs/connect-whatsapp.md)


Done. Enjoy your private AI. 🔒
