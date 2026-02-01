# OpenClaw Setup

OpenClaw is an AI agent framework. You point it at a local model (like LM Studio) and it can automate tasks on your computer - click things, run commands, manage files, whatever you teach it to do.

## Before Starting

You need:
- Node.js installed
- LM Studio running with a model
- LM Studio's Server tab active

## Install

```bash
npm install -g openclaw
```

Done.

Or from folder:
```bash
git clone https://github.com/OpenClawAI/OpenClaw.git
cd OpenClaw
npm install
npm start
```

## Configure

OpenClaw reads from `~/.openclaw/openclaw.json` (Linux/Mac) or `%USERPROFILE%\.openclaw\openclaw.json` (Windows).

Edit with:
```bash
nano ~/.openclaw/openclaw.json
```

Or just use Notepad on Windows.

Paste this:

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

Key parts:
- `mode: local` - no cloud
- `baseUrl` - where LM Studio is
- `token` - just pick something

## Run It

```bash
npx openclaw gateway --port 18789
```

Should say it's listening on 18789 and connected to LM Studio.

Open `http://localhost:18789`.

## Keep Running

Background (Linux/Mac):
```bash
npx openclaw gateway --port 18789 &
```

Windows: Use Task Scheduler or keep a terminal open.

## Issues

**Port 18789 taken**
```bash
npx openclaw gateway --port 19000
```

**Can't reach LM Studio**
- Server tab showing "Running"?
- Try: `curl http://127.0.0.1:1234/v1/models`

**Config file missing**
```bash
mkdir -p ~/.openclaw
echo '{}' > ~/.openclaw/openclaw.json
```

Then paste config above.

**Model not found**
Open LM Studio, download it. Make sure the name matches your config.

That's it.